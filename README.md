# Ansible refresh ##### Version 0.1
--------

##Table of Contents


####Install Vagrant
####Install Ruby
####Getting Started
--------

[install vagrant](https://www.vagrantup.com/downloads.html)

    vagrant -v
Vagrant 1.9.1

    ruby -v
ruby 2.2.2p95 (2015-04-13 revision 50295) [x86_64-darwin14]

you need version 2.2.+ of ruby for the vagrant-hostmanager


#### [install ruby] Install Ruby 2.2.+
--------

    brew install rbenv ruby-build

    rbenv install 2.2.2

    rbenv global 2.2.2

    echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile


#### [getting started] Getting Started:
--------
1. clone me
2. vagrant plugin install vagrant-hostmanager
3. vagrant up
4. vagrant ssh

 This should put you at the control host
 with access, by name, to other vms
 See Topology.pdf for network layout.


#### Install Ansible ( work in progress and is installed via vagrant up so can be skipped)
--------

Install Ansible and everything needed: ( Centos )

    yum install -y epel-release -y
    yum update
    yum install git python python-devel python-pip openssl ansible -y

Check if it works:

    ansible --version


#### Ansible Config
--------

Change config file:  

    vim /etc/ansible/ansible.cfg  

uncomment

    inventory = /etc/ansible/hosts  
    sudo_user = root  

Setup hosts file:  

    mv /etc/ansible/hosts /etc/ansible/hosts.org  

    vim /etc/ansible/hosts  

[local]  
localhost  

`[<host group>]  
<ip or fqdn of host>  

[<host group>]  
<ip or fqdn of host>  
<ip or fqdn of host>`  

Setup Ansible user:  

    adduser ansible  
    passwd ansible  
    visudo  

ansible ALL=(ALL) NOPASSWD: ALL  
<repeat on the other ansible hosts>  

Setup SSH_Keys from Ansible server  

    su - ansible  
    ssh-keygen  
    ssh-copy-id localhost  
    ssh-copy-id <host name>  

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

check that version 2.2+ is installed

    ansible --version

ansible 2.2.0.0



#### Commands
--------

(ansible) (group or host) (sudo) (arbitrary) (command)

    ansible app -s -a "service apache2 restart"

runs ping against all hosts

    ansible all -m ping

Under the "facts" directory will be a file for each host in GROUP

    ansible [GROUP] -m setup --tree "facts

installs the latest telnet package

    ansible app -s -m apt -a "name=telnet state=latest"


#### Command Sheet ( merge me with commands above)
--------

Ansible options:
-s = sudo
-m = module
-a "<command>"= action/command

User Modules:
- ping
- copy
- apt and/or yum
- user

Run ping on all hosts:

    ansible all -m ping

Run ls -al on all hosts:

    ansible all -a "ls -al"

Run command as root:

    ansible all -s -a "cat /var/log/messages"

Copy file from local to host:

    ansible <host group1> -m copy -a "src=<filenaam> dest=</folder/file>"

Install latest version of a package:

    ansible <host group> -s -m yum/apt -a "name=elinks state=latest"

Remove a package:

    ansible <host group> -s -m yum/apt -a "name=elinks state=absent"

Add user:  

    ansible <host group> -s -m user -a "name=test"

Remove user without home dir:  

    ansible <host group> -s -m user -a "name=test state=absent"



#### Other notes
--------

Return code with ansible command  
When you run a ansible command you have a return code like

    rc=0 it's if true
    rc=1 it's if false

#### Links
--------

https://www.vagrantup.com  
https://www.vagrantup.com/downloads.html  
https://github.com/devopsgroup-io/vagrant-hostmanager  
https://github.com/willthames/ansible-lint  
https://atom.io/packages/linter-ansible-linting  
http://erikzaadi.com/2015/11/15/linting-ansible-yaml-in-vim/  
http://docs.ansible.com/ansible/index.html  
http://docs.ansible.com/ansible/playbooks.html  
http://docs.ansible.com/ansible/playbooks_best_practices.html <-- READ ME  
https://github.com/ansible/ansible-examples  
