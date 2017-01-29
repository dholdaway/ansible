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

using LIMIT on a playbook run to host

    ansible-playbook site.yml --limit app01

Using TAGS

list

    ansible-playbook site.yml --list-tags

```
    playbook: site.yml

      play #1 (all): all	TAGS: []
          TASK TAGS: []

      play #2 (control): control	TAGS: []
          TASK TAGS: [{{ 'packages' }}]

      play #3 (database): database	TAGS: []
          TASK TAGS: []

      play #4 (webserver): webserver	TAGS: []
          TASK TAGS: []

      play #5 (loadbalancer): loadbalancer	TAGS: []
          TASK TAGS: []
```

skip

    ansible-playbook site.yml --skip-tags "packages"

do

    ansible-playbook site.yml --tags "package"


using Limit and tags

    ansible-playbook site.yml --limit lb01 --tags "configure"
```
PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [lb01]

PLAY [loadbalancer] ************************************************************

TASK [nginx : configure nginx sites] *******************************************
ok: [lb01] => (item={'key': u'myapp', 'value': {u'frontend': 80, u'backend': 80}})

TASK [nginx : get active sites] ************************************************
ok: [lb01]

TASK [nginx : de-activate sites] ***********************************************
skipping: [lb01] => (item=myapp)

TASK [nginx : activate nginx sites] ********************************************
ok: [lb01] => (item={'key': u'myapp', 'value': {u'frontend': 80, u'backend': 80}})

PLAY RECAP *********************************************************************
lb01                       : ok=4    changed=0    unreachable=0    failed=0
```

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

Gathering Facts with Filter inside a Playbook

But you can disable the default behavior and call setup yourself with the filter parameter.

```
- hosts: all
  sudo: yes
  gather_facts: False
  tasks:
   - setup:
       filter: ansible_*
```

Since you're working on a role and might not want to have this setup call in your role, you could make use of pre_tasks.

```
- hosts: all
  sudo: yes
  gather_facts: False
  pre_tasks:
   - setup:
       filter: ansible_*
  roles:
   - your_role_here
```

playbook using Selective Removal: shell, register, with_items, when
```
---
- name: install tools
  apt: name={{item}} state=present update_cache=yes
  with_items:
    - python-httplib2

- name: install nginx
  apt: name=nginx state=present update_cache=yes

- name: configure nginx sites
  template: src=nginx.conf.j2 dest=/etc/nginx/sites-available/{{ item.key }} mode=0644
  with_dict: "{{ sites }}"
  notify: restart nginx

- name: get active sites
  shell: ls -1 /etc/nginx/sites-enabled
  register: active

- name: de-activate sites
  file: path=/etc/nginx/sites-enabled/{{ item }} state=absent
  with_items: active.stdout_lines
  when: item not in sites
  notify: restart nginx

- name: activate nginx sites
  file: src=/etc/nginx/sites-available/{{ item.key }} dest=/etc/nginx/sites-enabled/{{ item.key }} state=link
  with_dict: " {{ sites }}"
  notify: restart nginx

- name: ensure nginx started
  service: name=nginx state=started enabled=yes
```
--------
### Roles
--------

Roles are a way to reuse functionality by putting all of the logic for a particular use case in the same place. As an example if you have tasks that you might run on all servers regardless of what the server is used for, that logic might be put in a role called “common.”

And if you have logic to configure a web server, then you may have another role named “webserver.” And whenever you need to install a new web application, you can include these two roles in your playbook.

ansible-galaxy <nameofrole> init

Roles have a directory structure like this:
```
rolename
 - files
 - handlers
 - meta
 - templates
 - tasks
 - vars
```

Within each directory, Ansible will search for and read any Yaml file called main.yml automatically.


--------
### Vault
--------
    ansible-vault create <name>

type a password

create a yml file

```
---
vault_db_pass: <any string>
```

    ansible-vault edit vault

enter password to open file  

ERROR! Decryption failed on /vagrant/group_vars/all/vault

    ansible-playbook XXX.yml --ask-vault-pass

or

    echo "password" > ~/.vault_pass.txt
    chmod 0600 !$

and stash it in the ansible.cfg

```
[defaults]
inventory = ./dev
vault_password_file = ~/.vault_pass.txt
```

    ansible-vault -h
```
Usage: ansible-vault [create|decrypt|edit|encrypt|rekey] \
      [--help] [options] file_name

Options:
  -h, --help  show this help message and exit
```

For the most part, we'll use ansible-vault create|edit /path/to/file.yml. Here, however, are all of the available commands:

create - Create a new file and encrypt it
decrypt - Create a plaintext file from an encrypted file
edit - Edit an already-existing encrypted file
encrypt - Encrypt an existing plain-text file
rekey - Set a new password on a encrypted file

------
### Troubleshooting Ansible
------

ansible to prompt and ask us which steps to run

    ansible-playbook site.yml --step

```
PLAY [all] *********************************************************************
Perform task: TASK: setup (N)o/(y)es/(c)ontinue: y
```

    ansible-playbook site.yml --start-at-task "activate nginx sites"

```
PLAY [all] *********************************************************************

PLAY [control] *****************************************************************

PLAY [database] ****************************************************************

PLAY [webserver] ***************************************************************

PLAY [loadbalancer] ************************************************************

TASK [nginx : activate nginx sites] ********************************************
ok: [lb01] => (item={'key': u'myapp', 'value': {u'frontend': 80, u'backend': 80}})

TASK [nginx : ensure nginx started] ********************************************
ok: [lb01]

PLAY RECAP *********************************************************************
lb01                       : ok=2    changed=0    unreachable=0    failed=0
```

if a host fails ansible generates a limit file for you to re-run.

    ansible-playbook site.yml --limit @xxx.retry

check syntax of yml file

    ansible-playbook --syntax-check site.yml

Dry Run

    ansible-playbook --check site.yml

note that not all modules are support, plus it wont gather facts

debugging

ansible has a debug module

this is our task
```
- name: get active sites
  shell: ls -1 /etc/nginx/sites-enabled
  register: active
  changed_when: "active.stdout_lines != sites.keys()"
  tags: [ 'configure' ]

- debug: var=active.stdout_lines
```

    ansible-playbook site.yml --limit lb01 --start-at-task "get active sites"

```
PLAY [all] *********************************************************************

PLAY [loadbalancer] ************************************************************

TASK [nginx : get active sites] ************************************************
ok: [lb01]

TASK [nginx : debug] ***********************************************************
ok: [lb01] => {
    "active.stdout_lines": [
        "myapp"
    ]
}

TASK [nginx : de-activate sites] ***********************************************
skipping: [lb01] => (item=myapp)

TASK [nginx : activate nginx sites] ********************************************
ok: [lb01] => (item={'key': u'myapp', 'value': {u'frontend': 80, u'backend': 80}})

TASK [nginx : ensure nginx started] ********************************************
ok: [lb01]

PLAY RECAP *********************************************************************
lb01                       : ok=4    changed=0    unreachable=0    failed=0
```

- debug: var=vars

is also useful for printing all the vars within the role.


--------
### Other notes
--------

Variable Precedence: Where Should I Put A Variable?

```
In 2.x, we have made the order of precedence more specific (with the last listed variables winning prioritization):

role defaults [1]
inventory vars [2]
inventory group_vars
inventory host_vars
playbook group_vars
playbook host_vars
host facts
play vars
play vars_prompt
play vars_files
registered vars
set_facts
role and include vars
block vars (only for tasks in block)
task vars (only for the task)
extra vars (always win precedence)
Basically, anything that goes into “role defaults” (the defaults folder inside the role) is the most malleable and easily overridden. Anything in the vars directory of the role overrides previous versions of that variable in namespace. The idea here to follow is that the more explicit you get in scope, the more precedence it takes with command line -e extra vars always winning. Host and/or inventory variables can win over role defaults, but not explicit includes like the vars directory or an include_vars task.
```

Return code with ansible command  
When you run a ansible command you have a return code like

    rc=0 it's if true
    rc=1 it's if false


Which syntax would loop through and render the elements of the list variable "backends" in a Jinja2 template?

    {% for server in backends %} {{ server }} {% endfor %}

For loop is in '{%', ends with 'endfor' and the variable is rendered with '{{'

one error on ansible 2.2.0.0 register.stdout_lines format. Incorrect syntax:

    with_items: active.stdout_lines

Correct syntax:

    with_items: "{‌{active.stdout_lines}}"

How could you define a variable value and be absolutely sure that it would not be overridden anywhere else by Ansible?

    Pass the variable using the `-e` or `--extra-vars`parameter when running `ansible-playbook`.

What ad-hoc command would you run to determine the facts available for a server?

    ansible -m setup

The "setup" module will query all facts on a host and return them.

to time the how long a playbook takes

    time ansible-playbook <playbook>
```
    real	0m9.064s
    user	0m2.771s
    sys	0m1.263s
```

this is useful for optimisation

Templates

Templates at their core are a way to copy files to a remote server. However the difference between templates and static files is that templates will be processed before they’re copied to the remote host. And this allows you to include variables, conditionals, loops, etc. Ansible uses Jinja2 as its template engine.

Handlers

Handlers are basically tasks that are run when some event happens. As an example if you change a web server’s configuration file, you’ll need to restart the web server service. This is an ideal use case for handlers.

--------
### Playbooks Database (its probably already been written)
--------
https://galaxy.ansible.com/

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
https://docs.ansible.com/ansible/playbooks_variables.html
https://liquidat.wordpress.com/2016/01/26/howto-introduction-to-ansible-variables/
http://stackoverflow.com/questions/22522985/how-can-i-write-variables-inside-the-tasks-file-in-ansible
http://stackoverflow.com/questions/30662069/how-can-i-pass-variable-to-ansible-playbook-in-the-command-line
http://docs.ansible.com/ansible/playbooks_loops.html#looping-over-hashes
https://docs.ansible.com/ansible/playbooks_vault.html
-------
### Trouble Shooting Links
-------
https://docs.ansible.com/ansible/uri_module.html
http://sgargan.blogspot.co.uk/2013/10/troubleshooting-ssh-connections-in.html
http://stackoverflow.com/questions/37425078/ansible-failed-to-connect-to-the-host-via-ssh-error
http://stackoverflow.com/questions/37213551/ansible-ssh-connection-fail
