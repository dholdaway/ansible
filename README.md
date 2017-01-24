# Ansible refresh
##### Version 0.1
--------
##Table of Contents
####Install Vagrant
####Install Ruby
####Getting Started
####Install Ansible
####Ansible Config
####Ansible Commands
####Ansible Command Sheet ( merge me with commands above)
####Other Notes
####Links
--------

### Install Vagrant
--------
[install vagrant](https://www.vagrantup.com/downloads.html)

    vagrant -v
Vagrant 1.9.1

    ruby -v
ruby 2.2.2p95 (2015-04-13 revision 50295) [x86_64-darwin14]

you need version 2.2.+ of ruby for the vagrant-hostmanager

--------
### Install Ruby 2.2.+
--------

    brew install rbenv ruby-build

    rbenv install 2.2.2

    rbenv global 2.2.2

    echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile

--------
### Getting Started:
--------
1. clone me
2. vagrant plugin install vagrant-hostmanager
3. vagrant up
4. vagrant ssh

 This should put you at the control host
 with access, by name, to other vms
 See Topology.pdf for network layout.


--------
### Install Ansible ( work in progress and is installed via vagrant up so can be skipped)
--------

Install Ansible and everything needed: ( Centos )

    yum install -y epel-release -y
    yum update
    yum install git python python-devel python-pip openssl ansible -y

Check if it works:

    ansible --version

--------
### Ansible Config
--------

Change config file:  

    vim /etc/ansible/ansible.cfg  

uncomment

    inventory = /etc/ansible/hosts  
    sudo_user = root  

Setup hosts file:  

    mv /etc/ansible/hosts /etc/ansible/hosts.org  

    vim /etc/ansible/hosts  

```
[local]  
localhost  

[<host group>]  
<ip or fqdn of host>  

[<host group>]  
<ip or fqdn of host>  
<ip or fqdn of host>`  
```

Setup Ansible user:  

    adduser ansible  
    passwd ansible  
    visudo  

```
ansible ALL=(ALL) NOPASSWD: ALL
```  
### <repeat on the other ansible hosts>  

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


--------
### Commands
--------

(ansible) (group or host) (sudo) (arbitrary) (command)

    ansible app -s -a "service apache2 restart"

runs ping against all hosts

    ansible all -m ping

Under the "facts" directory will be a file for each host in GROUP

    ansible [GROUP] -m setup --tree "facts

installs the latest telnet package

    ansible app -s -m apt -a "name=telnet state=latest"

--------
### Command Sheet ( merge me with commands above)
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


    ansible webserver -s -m apt -a "name=nginx state=absent"

```
app02 | SUCCESS => {
    "changed": true,
    "stderr": "",
    "stdout": "Reading package lists...\nBuilding dependency tree...\nReading state information...\nThe following packages were automatically installed and are no longer required:\n  libxslt1.1 nginx-common nginx-core\nUse 'apt-get autoremove' to remove them.\nThe following packages will be REMOVED:\n  nginx\n0 upgraded, 0 newly installed, 1 to remove and 156 not upgraded.\nAfter this operation, 96.3 kB disk space will be freed.\n(Reading database ... 65250 files and directories currently installed.)\nRemoving nginx (1.4.6-1ubuntu3.7) ...\n",
    "stdout_lines": [
        "Reading package lists...",
        "Building dependency tree...",
        "Reading state information...",
        "The following packages were automatically installed and are no longer required:",
        "  libxslt1.1 nginx-common nginx-core",
        "Use 'apt-get autoremove' to remove them.",
        "The following packages will be REMOVED:",
        "  nginx",
        "0 upgraded, 0 newly installed, 1 to remove and 156 not upgraded.",
        "After this operation, 96.3 kB disk space will be freed.",
        "(Reading database ... 65250 files and directories currently installed.)",
        "Removing nginx (1.4.6-1ubuntu3.7) ..."
    ]
}
app01 | SUCCESS => {
    "changed": true,
    "stderr": "",
    "stdout": "Reading package lists...\nBuilding dependency tree...\nReading state information...\nThe following packages were automatically installed and are no longer required:\n  libxslt1.1 nginx-common nginx-core\nUse 'apt-get autoremove' to remove them.\nThe following packages will be REMOVED:\n  nginx\n0 upgraded, 0 newly installed, 1 to remove and 156 not upgraded.\nAfter this operation, 96.3 kB disk space will be freed.\n(Reading database ... 65250 files and directories currently installed.)\nRemoving nginx (1.4.6-1ubuntu3.7) ...\n",
    "stdout_lines": [
        "Reading package lists...",
        "Building dependency tree...",
        "Reading state information...",
        "The following packages were automatically installed and are no longer required:",
        "  libxslt1.1 nginx-common nginx-core",
        "Use 'apt-get autoremove' to remove them.",
        "The following packages will be REMOVED:",
        "  nginx",
        "0 upgraded, 0 newly installed, 1 to remove and 156 not upgraded.",
        "After this operation, 96.3 kB disk space will be freed.",
        "(Reading database ... 65250 files and directories currently installed.)",
        "Removing nginx (1.4.6-1ubuntu3.7) ..."
    ]
}
```

Add user:  

    ansible <host group> -s -m user -a "name=test"

Remove user without home dir:  

    ansible <host group> -s -m user -a "name=test state=absent"

Deploying From Source Control Deploy your webapp straight from git:

    ansible webservers -m git -a "repo=git://foo.example.org/repo.git dest=/srv/myapp version=HEAD"

Since Ansible modules can notify change handlers it is possible to tell Ansible to run specific tasks when the code is updated, such as deploying Perl/Python/PHP/Ruby directly from git and then restarting apache.

Managing Services
    Ensure a service is started on all webservers:

    ansible webservers -m service -a "name=httpd state=started"

Alternatively, restart a service on all webservers:

    ansible webservers -m service -a "name=httpd state=restarted"

Ensure a service is stopped:

    ansible webservers -m service -a "name=httpd state=stopped"


--------
### Playbooks
--------

run playbook

    ansible-playbook <locationofplaybook.yaml>

Example playbook  
installs curl on control host using sudo

```
---
- hosts: control
become: true
tasks:
- name: install tools
  apt: name={{item}} state=present update_cache=yes
  with_items:
    - curl
```

Example playbook with items
installs a number of web items

```
---
- hosts: webserver
  become: true
  tasks:
    - name: install web components
      apt: name={{item}} state=present update_cache=yes
      with_items:
        - apache2
        - libapache2-mod-wsgi
        - python-pip
        - python-virtualenv
        - python-mysqldb
```

Example playbook with wait for

```
---
- hosts: loadbalancer
  become: true
  tasks:
    - name: verify nginx service
      command: service nginx status

    - name: verify nginx is listening on 80
      wait_for: port=80 timeout=1
```



--------
### Other notes
--------

Return code with ansible command  
When you run a ansible command you have a return code like

    rc=0 it's if true
    rc=1 it's if false


Which syntax would loop through and render the elements of the list variable "backends" in a Jinja2 template?

    {% for server in backends %} {{ server }} {% endfor %}

For loop is in '{%', ends with 'endfor' and the variable is rendered with '{{'

--------
### Links
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
https://docs.ansible.com/ansible/intro_patterns.html
http://docs.ansible.com/ansible/intro_adhoc.html#managing-packages

-------
### Trouble Shooting Links
-------
https://docs.ansible.com/ansible/uri_module.html
http://sgargan.blogspot.co.uk/2013/10/troubleshooting-ssh-connections-in.html
http://stackoverflow.com/questions/37425078/ansible-failed-to-connect-to-the-host-via-ssh-error
http://stackoverflow.com/questions/37213551/ansible-ssh-connection-fail
