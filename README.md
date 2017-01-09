# README
#
# Getting Started:
# 1. vagrant plugin install vagrant-hostmanager
# 2. vagrant up
# 3. vagrant ssh
#
# This should put you at the control host
#  with access, by name, to other vms
# See Topology.pdf for network layout.

# notes

    vagrant -v
Vagrant 1.9.1

    ruby -v
ruby 2.2.2p95 (2015-04-13 revision 50295) [x86_64-darwin14]

https://www.vagrantup.com

https://github.com/devopsgroup-io/vagrant-hostmanager

The best way to get Ansible for Ubuntu is to add the project's PPA (personal package archive) to your system.

To do this effectively, we need to install the software-properties-common package, which will give us the ability to work with PPAs easily. (This package was called python-software-properties on older versions of Ubuntu.)

    sudo apt-get update
    sudo apt-get install software-properties-common
Once the package is installed, we can add the Ansible PPA by typing the following command:

sudo apt-add-repository ppa:ansible/ansible
Press ENTER to accept the PPA addition.

Next, we need to refresh our system's package index so that it is aware of the packages available in the PPA. Afterwards, we can install the software:

    sudo apt-get update
    sudo apt-get install ansible
We now have all of the software required to administer our servers through Ansible

    ansible --version
ansible 2.2.0.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = Default w/o overrides
