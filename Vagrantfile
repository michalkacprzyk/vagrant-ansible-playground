# mk (c) 2019
# vi: set ft=ruby :
#
# Automatically create 8 VMs with a private network and a DNS server.
# This setup can serve as an experimentation playground.
# For example for the Mastering Ansible Udemy course, it was inspired with.
#
# This file is not very Don'tRepeatYourself on purpose,
#  so hopefully less advanced learners can understand it easier

# Force locales on ssh connections
ENV["LC_ALL"] = "en_US.UTF-8"

# 2 - configuration file version, do not change
Vagrant.configure("2") do |config|

# Global provider settings
  config.vm.provider "virtualbox" do |vb|
     # VMs CPU
     vb.cpus = 1
     # VMs RAM
     vb.memory = "256"
     # Use linked clones to save time and disk space 
     vb.linked_clone = true
     # Workaround VirtualBox E1000 NIC bug
     vb.default_nic_type = "virtio"
  end

# dnsmasq
  config.vm.define "dnsmasq" do |dns|
    dns.vm.box = "ubuntu/xenial64"
    dns.vm.network "private_network", ip: "192.168.200.40"
    dns.vm.provision :shell, inline: <<-SHELL
      echo "dnsmasq" > /etc/hostname
      hostname "dnsmasq"
      apt-get update
      apt-get install -y dnsmasq
      echo "server=8.8.8.8" >> /etc/dnsmasq.d/google

      echo "192.168.200.40 dnsmasq" >> /etc/hosts
      echo "192.168.200.41 ubuntu-c" >> /etc/hosts
      echo "192.168.200.42 ubuntu1" >> /etc/hosts
      echo "192.168.200.43 ubuntu2" >> /etc/hosts
      echo "192.168.200.44 ubuntu3" >> /etc/hosts
      echo "192.168.200.45 centos1" >> /etc/hosts
      echo "192.168.200.46 centos2" >> /etc/hosts
      echo "192.168.200.47 centos3" >> /etc/hosts

      systemctl restart dnsmasq.service
      systemctl status dnsmasq.service
    SHELL
  end

# ubuntu1, ubuntu2, ubuntu 3
  (1..3).each do |i|
    config.vm.define "ubuntu#{i}" do |node|
      node.vm.box = "ubuntu/xenial64"
      node.vm.network "private_network", ip: "192.168.200.4#{i+1}"
      node.vm.provision :shell, inline: <<-SHELL
        hostname "ubuntu#{i}"
        echo "ubuntu#{i}" > /etc/hostname
        echo "      dns-nameserver 192.168.200.40" >> /etc/network/interfaces
        systemctl restart networking.service
        ping -c 1 dnsmasq

        apt-get update
        apt-get install python -y
      SHELL
    end
  end

# centos1, centos2, centos3
  (1..3).each do |i|
    config.vm.define "centos#{i}" do |node|
      node.vm.box = "centos/7"
      node.vm.network "private_network", ip: "192.168.200.4#{4+i}"
      node.vm.provision :shell, inline: <<-SHELL
        # Networking config
        hostname "centos#{i}"
        echo "centos#{i}" > /etc/hostname
        echo "nameserver 192.168.200.40" >> /etc/resolv.conf
        ping -c 1 dnsmasq

        # Reassemble the masteringansible setup
        printf "password\npassword" | passwd
        sed -i 's/PasswordAuthentication no/#PasswordAuthentication no/' /etc/ssh/sshd_config
        service sshd restart
      SHELL
    end
  end

# ubuntu-c
  config.vm.define "ubuntuc" do |ubuntuc|
    ubuntuc.vm.box = "ubuntu/xenial64"
    ubuntuc.vm.network "private_network", ip: "192.168.200.41"

    # As a superuser
    ubuntuc.vm.provision :shell, inline: <<-SHELL
      # Networking config
      hostname "ubuntu-c"
      echo "ubuntu-c" > /etc/hostname
      echo "      dns-nameserver 192.168.200.40" >> /etc/network/interfaces
      systemctl restart networking.service
      ping -c 1 dnsmasq
     
      # Ansible from official repositories
      apt-get update
      apt-get install software-properties-common git -y
      apt-add-repository ppa:ansible/ansible -y
      apt-get install ansible -y
      ansible --version
    SHELL

    # As a normal user
    ubuntuc.vm.provision :shell, privileged: false, inline: <<-SHELL
      # Passwordless ssh to all hosts
      ssh-keygen -f /home/vagrant/.ssh/id_rsa -P ""
      for M in ubuntu1 ubuntu2 ubuntu3 centos1 centos2 centos3; do
        ssh-copy-id -f \
           -o "StrictHostKeyChecking no" \
           -o "IdentityFile /vagrant/.vagrant/machines/${M}/virtualbox/private_key" \
           -i /home/vagrant/.ssh/id_rsa.pub vagrant@${M}
      done
 
      # Masteringansible courseware
      git clone https://github.com/spurin/masteringansible
      cd masteringansible/01*/05*/03
      ansible all -m ping -o
    SHELL
  end

end
