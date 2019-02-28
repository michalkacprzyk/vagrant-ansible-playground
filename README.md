# vagrant-ansible-playground

## What is?

Automated creation of multiple Virtual Machines, connected with a private network, a simple DNS server and passwordless ssh.

## What for?

Can be used as a playground for practicing ansible or similar tools. It was inspired by quite a heavy download, originally needed by [Ansible Automation Masterclass](https://www.udemy.com/ansible-ansible-automation-masterclass-2-in-1) Udemy course

## How to?
Assuming that [VirtualBox](https://www.virtualbox.org/wiki/Downloads) and [Vagrant](https://www.vagrantup.com/downloads.html) are installed, and we are in the cloned directory of this repository 

```bash
# Peruse the Vagrantfile, making sure that it looks quite sensible
vim Vagrantfile

# Create the entire playground
vagrant up

# Connect to the control host
vagrant ssh ubuntuc

# Stop the entire playground
vagrant halt

# Destroy (it is easy enough to recreate)
vagrant destroy
```

## What if?
In my case *vagrant up* ends after 9 minutes with the following message:
```
ubuntuc: ubuntu1 | SUCCESS => {"changed": false, "ping": "pong"}
ubuntuc: ubuntu2 | SUCCESS => {"changed": false, "ping": "pong"}
ubuntuc: centos1 | SUCCESS => {"changed": false, "ping": "pong"}
ubuntuc: centos2 | SUCCESS => {"changed": false, "ping": "pong"}
ubuntuc: centos3 | SUCCESS => {"changed": false, "ping": "pong"}
ubuntuc: ubuntu3 | SUCCESS => {"changed": false, "ping": "pong"}
```
if it is different for you, have fun bug-hunting!
