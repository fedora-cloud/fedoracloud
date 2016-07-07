Atomic image examples
======================

This chapter will contain various examples related to Atomic project.

Setting up Fedora Atomic cluster with Kubernetes and Vagrant
-------------------------------------------------------------

You can use Vagrant to setup a Fedora Atomic cluster. Use the following steps to set it up.

::

    $ git clone https://github.com/kubernetes/contrib.git
    $ cd contrib/ansible/vagrant
    $ export OS_IMAGE=fedoraatomic # (regular fedora, regular centos, centos atomic are other options)
    $ vagrant up --no-provision --provider=libvirt # (virtualbox, openstack and aws are other provider options)
    $ vagrant provision kube-master

This should get you a working Atomic cluster ready. For more details follow `this blog post <http://www.projectatomic.io/blog/2015/09/clustering-atomic-hosts-with-kubernetes-ansible-and-vagrant/>`_.


Setting up Fedora Atomic cluster with Kubernetes and preinstalled hosts
------------------------------------------------------------------------

In case you don't want to use Vagrant, you can systems preinstalled with Fedora Atomic, and then update
the inventory file (check the section above for git repo link), and use the same.
