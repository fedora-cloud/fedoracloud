Cloud Base Image examples
=========================

This chapter will contain various examples related to Cloud Base Image.

How to do docker-storage-setup in Fedora 22 cloud image?
--------------------------------------------------------

docker-storage-setup helps to create a LVM thin pool, which can be then used by
docker for storage of containers, and images. By starting docker, it
automatically starts this service. We can also make sure that it uses a
specific block device, and volume group. In this example I am running Fedora 22
Cloud Base image on an Openstack environment, I added a new volume */dev/vdb*
to the instance.

::

    # cat <<EOF > /etc/sysconfig/docker-storage-setup
    DEVS=/dev/vdb
    VG=docker-vg
    EOF
    # sudo docker-storage-setup
      Volume group "vda1" not found
      Cannot process volume group vda1
    Checking that no-one is using this disk right now ... OK

    Disk /dev/vdb: 5 GiB, 5379194880 bytes, 10506240 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0xc8ed8872

    Old situation:

    >>> Script header accepted.
    >>> Created a new DOS disklabel with disk identifier 0x39ca0d62.
    Created a new partition 1 of type 'Linux LVM' and of size 5 GiB.
    /dev/vdb2: 
    New situation:

    Device     Boot Start      End  Sectors Size Id Type
    /dev/vdb1        2048 10506239 10504192   5G 8e Linux LVM

    The partition table has been altered.
    Calling ioctl() to re-read partition table.
    Syncing disks.
      Physical volume "/dev/vdb1" successfully created
      Volume group "docker-vg" successfully created
      Rounding up size to full physical extent 8.00 MiB
      Logical volume "docker-poolmeta" created.
      Logical volume "docker-pool" created.
      WARNING: Converting logical volume docker-vg/docker-pool and docker-vg/docker-poolmeta to pool's data and metadata volumes.
      THIS WILL DESTROY CONTENT OF LOGICAL VOLUME (filesystem etc.)
      Converted docker-vg/docker-pool to thin pool.
      Logical volume "docker-pool" changed

How to build a network router and firewall with Fedora 22 and systemd-networkd?
--------------------------------------------------------------------------------

Major Hayden explains that in `this blog post <https://major.io/2015/08/27/build-a-network-router-and-firewall-with-fedora-22-and-systemd-networkd/>`_.
