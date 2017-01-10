# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.hostmanager.enabled = true

  config.vm.box = "ubuntu/trusty64"
#   config.ssh.username = 'root'
# config.ssh.password = 'vagrant'
# config.ssh.insert_key = 'true'

  config.vm.define "control", primary: true do |h|
    h.vm.network "private_network", ip: "192.168.135.10"
    h.vm.provision :shell, :inline => <<'EOF'
if [ ! -f "/home/vagrant/.ssh/id_rsa" ]; then
  ssh-keygen -t rsa -N "" -f /home/vagrant/.ssh/id_rsa
fi
cp /home/vagrant/.ssh/id_rsa.pub /vagrant/control.pub
cp ~vagrant/.ssh/authorized_keys /root/.ssh
sudo apt-get update -y
sudo apt-get install software-properties-common -y
sudo apt-add-repository ppa:ansible/ansible -y
sudo apt-get update -y
sudo apt-get install ansible -y

ansible -v

cat << 'SSHEOF' > /home/vagrant/.ssh/config
Host *
  StrictHostKeyChecking no
  UserKnownHostsFile=/dev/null
SSHEOF

chown -R vagrant:vagrant /home/vagrant/.ssh/
EOF
  end

  config.vm.define "lb01" do |h|
    h.vm.network "private_network", ip: "192.168.135.101"
    config.vm.network "forwarded_port", guest: 8080, host: 8080
    h.vm.provision :shell, inline: 'cat /vagrant/control.pub >> /home/vagrant/.ssh/authorized_keys'
    h.vm.provision :shell, inline: 'cp ~vagrant/.ssh/authorized_keys /root/.ssh'
  end

  config.vm.define "app01" do |h|
    h.vm.network "private_network", ip: "192.168.135.111"
    config.vm.network "forwarded_port", guest: 8080, host: 8080
    h.vm.provision :shell, inline: 'cat /vagrant/control.pub >> /home/vagrant/.ssh/authorized_keys'
    h.vm.provision :shell, inline: 'cp ~vagrant/.ssh/authorized_keys /root/.ssh'
  end

  config.vm.define "app02" do |h|
    h.vm.network "private_network", ip: "192.168.135.112"
    config.vm.network "forwarded_port", guest: 8080, host: 8080
    h.vm.provision :shell, inline: 'cat /vagrant/control.pub >> /home/vagrant/.ssh/authorized_keys'
    h.vm.provision :shell, inline: 'cp ~vagrant/.ssh/authorized_keys /root/.ssh'
  end

  config.vm.define "db01" do |h|
    h.vm.network "private_network", ip: "192.168.135.121"
    h.vm.provision :shell, inline: 'cat /vagrant/control.pub >> /home/vagrant/.ssh/authorized_keys'
    h.vm.provision :shell, inline: 'cp ~vagrant/.ssh/authorized_keys /root/.ssh'
  end
end
