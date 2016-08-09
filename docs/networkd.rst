This Documenation is created with the help of `Major Hayden <https://major.io/>`_. Thanks to Major Hayden :).

Networkd
=========

This chapter shows how to convert Network manger to Networkd and various use cases of Networkd

Comparing systemd-networkd and NetworkManager
---------------------------------------------

- NetworkManager has been around for quite some time and systemd-networkd is relatively new.
- Re-configuring existing network interfaces is challenging for both NetworkManager and systemd-networkd
- Accessing raw systemd-networkd configuration files is more straightforward than NetworkManager
- systemd-networkd's memory and CPU usage is extremely low (good for containerized environments)
- systemd-networkd can handle various sysctl activities automatically, like IP forwarding
- Tunnels and Overlays
  - NetworkManager has more options for advanced tunnels, like vpnc, openconnect, and openvpn
  - systemd-networkd makes gre, vlan, vxlan, and other overlay technologies much easier to implement
- NetworkManager's logging is more verbose by default, which can be good for troubleshooting
- systemd-networkd is meant to be configured without a GUI

Configuration
-------------

Fedora Cloud Base Image. Download it from `here <https://getfedora.org/en/cloud/download/>`_.

Switching from NetworkManager to systemd-networkd
-------------------------------------------------

Start by ensuring that NetworkManager and network scripts don't start on reboot:

::

    # systemctl disable NetworkManager
    # systemctl disable network

Ensure that systemd-networkd starts on the next boot:

::

    # systemctl enable systemd-networkd

Enable the resolver and make a symlink:

::

    # systemctl enable systemd-resolved
    # systemctl start systemd-resolved
    # rm -f /etc/resolv.conf
    # ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

Be sure to configure your network interfaces in ``/etc/systemd/network`` and then reboot.

systemd-networkd use cases
--------------------------

Here are some sample use cases for systemd-networkd and example configurations.

**Simple DHCP on a single interface**

For an interface ``eth0``, a single ``.network`` file is needed:

::

    # cat /etc/systemd/network/eth0.network
    [Match]
    Name=eth0

    [Network]
    DHCP=yes

**Static address on a single interface**

For an interface ``eth0``, a single ``.network`` file is needed:

::

    # cat /etc/systemd/network/eth0.network
    [Match]
    Name=eth0

    [Network]
    Address=192.168.0.50/24
    Address=2001:db8:dead:beef::/64

    # These are optional but worth mentioning
    DNS=8.8.8.8
    DNS=8.8.4.4
    NTP=pool.ntp.org

You can also split up the addresses into separate blocks:

::

    # cat /etc/systemd/network/eth0.network
    [Match]
    Name=eth0

    [Network]
    DNS=8.8.8.8
    DNS=8.8.4.4
    NTP=pool.ntp.org

    [Address]
    Address=192.168.0.50/24

    [Address]
    Address=2001:db8:dead:beef::/64

Or add static routes:

::

    # cat /etc/systemd/network/eth0.network
    [Match]
    Name=eth0

    [Network]
    DNS=8.8.8.8
    DNS=8.8.4.4
    NTP=pool.ntp.org

    [Address]
    Address=192.168.0.50/24

    [Address]
    Address=2001:db8:dead:beef::/64

    [Route]
    Destination=10.0.10.0/24
    Gateway=192.168.50.1

    [Route]
    Destination=10.0.20.0/24
    Gateway=192.168.50.1

**Do DHCP on all network devices**

You can use wildcards almost anywhere in the ``[Match]`` block. For example, this will cause systemd-networkd to do DHCP on all interfaces:

::

    [Match]
    Name=eth*

    [Network]
    DHCP=yes

**Bridging**

Let's consider an example where we have ``eth0`` and we want to add it to a bridge. This could be handy for servers where you want to build containers or virtual machines and attach them to the network bridge.

Start by setting up our bridge interface, ``br0``:

::

    # cat /etc/systemd/network/br0.netdev
    [NetDev]
    Name=br0
    Kind=bridge

Now that we have a bridge device, let's configure the network for the bridge:

::

    # cat /etc/systemd/network/br0.network
    [Match]
    Name=br0

    [Network]
    IPForward=yes
    DHCP=yes

The ``IPForward=yes`` will take care of the sysctl forwarding setting for us ``(net.ipv4.conf.br0.forwarding = 1)`` automatically when the interface comes up.

Now, let's take the ethernet adapter and add it to the bridge:

::

    # cat /etc/systemd/network/eth0.network
    [Match]
    Name=eth0

    [Network]
    Bridge=br0

Simply reboot the system and it will come up with ``eth0`` as a port on ``br0``.

**Bonding**

Configuring a bonded interface is very similar to configuring a bridge. Start by setting up the individual network adapters:

::

    # /etc/systemd/network/ens9f0.network
    [Match]
    Name=ens9f0

    [Network]
    Bond=bond1

::

    # /etc/systemd/network/ens9f1.network
    [Match]
    Name=ens9f1

    [Network]
    Bond=bond1

Now we can create the network device for the bond:

::

    # /etc/systemd/network/bond1.netdev
    [NetDev]
    Name=bond1
    Kind=bond

    [Bond]
    Mode=802.3ad
    TransmitHashPolicy=layer3+4
    MIIMonitorSec=1s
    LACPTransmitRate=fast

Once the device is defined, let's add some networking to it:

::

    # /etc/systemd/network/bond1.network
    [Match]
    Name=bond1

    [Network]
    DHCP=yes
    BindCarrier=ens9f0 ens9f1

The **BindCarrier** is optional but recommended. It gives systemd-networkd the hint that if both bonded interfaces are offline, it should remove the bond configuration until one of the interfaces comes up again.

Status & Diagnostics
--------------------

All of the output from systemd-networkd will appear in your system journal. Any errors when setting up interfaces or configuring routes will be printed there. The ``networkctl`` command allows you to check your network devices at a glance. Here's an example of a fairly complicated network setup:

::

    # networkctl
    IDX LINK             TYPE               OPERATIONAL SETUP
      1 lo               loopback           carrier     unmanaged
      2 enp3s0           ether              off         unmanaged
      3 enp1s0f0         ether              degraded    configured
      4 enp1s0f1         ether              degraded    configured
      5 br1              ether              routable    configured
      6 br0              ether              routable    configured
      7 gre0             ipgre              off         unmanaged
      8 gretap0          ether              off         unmanaged
      9 gre-colocation   ipgre              routable    configured
     12 vlan100          ether              routable    configured
     13 tun1             none               routable    unmanaged
     14 tun0             none               routable    unmanaged
     15 vlan200          ether              routable    configured

You'll find two physical network cards (``enp1s0f0`` and ``enp1s0f1``) each attached to a bridge (``br0`` and ``br1``, respectively). The physical network adapters show up as degraded because they don't have network addresses directly assigned -- that assignment is done on the bridge. The ``gre0`` and ``gretap0`` devices are created automatically to handle the gre tunnel ``gre-colocation``. There are also two VLANs configured within systemd and attached to a bridge. The ``tun`` interfaces are OpenVPN interfaces and they are not configured by systemd-networkd (hence the unmanaged setup).

**Further Reading**

- `ArchLinux systemd-networkd documentation <https://wiki.archlinux.org/index.php/Systemd-networkd/>`_
- `Upstream systemd-networkd documentation <https://www.freedesktop.org/software/systemd/man/systemd-networkd.service.html/>`_
