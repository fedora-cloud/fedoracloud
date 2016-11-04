=======
Vagrant
=======

`Vagrant <https://www.vagrantup.com/>`_ is a useful development tool that lowers the barrier to
entry to new contributors of your project. With Vagrant, new contributors don't have to spend much
time configuring their development environment, but can quickly get one automatically provisioned
for them with a few simple commands.

Using Vagrant in Fedora
=======================

Fedora's Cloud working group provides Fedora Vagrant boxes for libvirt and Virtualbox. You can see
the Fedora 24 Vagrant images
`here <https://download.fedoraproject.org/pub/fedora/linux/releases/24/CloudImages/x86_64/images/>`_.

To quickly get started with Vagrant on a Fedora host, the Fedora developers have conveniently
packaged vagrant-libvirt and a handful of handy Vagrant plugins for you. All you need to do is to
install vagrant-libvirt and the plugins you wish to use, write a Vagrantfile for your project, and
type "vagrant up" to get started. Here's an example Vagrantfile::

 $ cat Vagrantfile
 # -*- mode: ruby -*-
 # vi: set ft=ruby :
 
 # On your host:
 # git clone https://github.com/fedora-infra/bodhi.git
 # cd bodhi
 # cp Vagrantfile.example Vagrantfile
 # vagrant up
 # vagrant ssh -c "cd /vagrant/; pserve development.ini --reload"
 
 Vagrant.configure(2) do |config|
   config.vm.box_url = "https://download.fedoraproject.org/pub/fedora/linux/releases/24/CloudImages/x86_64/images/Fedora-Cloud-Base-Vagrant-24-1.2.x86_64.vagrant-libvirt.box"
   config.vm.box = "f24-cloud-libvirt"
   config.vm.network "forwarded_port", guest: 80, host: 80
   config.vm.synced_folder ".", "/vagrant", type: "sshfs"
 
   config.vm.provider :libvirt do |domain|
       domain.cpus = 4
       domain.graphics_type = "spice"
       domain.memory = 1024
       domain.video_type = "qxl"
   end
 
   config.vm.provision "shell", inline: "echo hello_world > /home/vagrant/hello_world.txt"
 
   # Uncomment the following block if you have a playbook at devel/ansible/playbook.yml you want Vagrant to run on the guest for you
   # # Ansible needs the guest to have these
   # config.vm.provision "shell", inline: "sudo dnf install -y libselinux-python python2-dnf"
   #
   # config.vm.provision "ansible" do |ansible|
   #     ansible.playbook = "devel/ansible/playbook.yml"
   # end
 end

In this example, we're using the Fedora 24 libvirt box and we're assuming that your project has an
ansible playbook at the relative path ``devel/ansible/playbook.yml``. Writing ansible playbooks is
beyond the scope of this document, and you are free to use config.vm.provision lines to configure
your Vagrant guest if you like.

To get started with the above example, simply write the code to a file called Vagrantfile, install
``vagrant-libvirt`` and ``vagrant-sshfs``, and run ``vagrant up``::

 $ cd ~/devel/my_project
 $ vim Vagrantfile  # Write the above Vagrant code here
 $ sudo dnf install vagrant-libvirt vagrant-sshfs
 $ vagrant up

Once your guest is running, you can ssh into the guest. Your code directory from the host will be
shared into the guest at the path ``/vagrant``::

 $ vagrant ssh
 $ [vagrant@localhost ~]$ ls /vagrant/Vagrantfile
 /vagrant/Vagrantfile

Now you can edit your project on your host, and the changes you make will be shared into the guest's
``/vagrant`` folder live. This allows you to use your editor of choice (even graphical editors!) on
the host, while keeping everything that might "dirty" you host system contained in the guest virtual
machine. This example ``Vagrantfile`` has also set up a port forward from 80 in the guest to 80 on
the host, so if there were a web application listening in the guest on port 80, you could browse to
http://localhost on the host to access it.

When you are done with your Vagrant guest, you can destroy it::

 $ vagrant destroy

It is good practice to check in a ``Vagrantfile.example`` file in your project's source code, rather
than the ``Vagrantfile`` itself. This allows new developers a quick way to get started by just
copying your example into place, but it also allows each contributor to make the changes they prefer
to their individual Vagrantfiles.
