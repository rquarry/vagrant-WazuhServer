# What is this?

This Vagrantfile will create 1 Ubuntu 20.04 VM with Wazuh server installed. Future plans are to add multiple agent VM's to demonstrate a small "observability" lab setup.

* Host1 - wazuh.server.com (192.168.29.2)

## Requirements:

* Internet connection is a must!
* Make sure the VT support is enabled on your BIOS
* Vagrant - 1.9.x or higher
* Vagrant plugins - vagrant-proxyconf - needed if you are running behind proxy
* Ansible: latest
* Virtualbox: latest


# Note: if the build fail because of the hypervisor errors. You need to follow this steps.

First find out the name of the hypervisor:
```
$ lsmod | grep kvm
kvm_intel             204800  6
kvm                   593920  1 kvm_intel
irqbypass              16384  1 kvm
```
The one we're interested in is kvm_intel. You might have another.

Blacklist the hypervisor (run the following as root):
```
$ echo 'blacklist kvm-intel' >> /etc/modprobe.d/blacklist.conf
```
Restart your machine and try running vagrant again.


## Other files included:
- bootstrap-node.sh - Sets up the hosts file on each VM with IP entries
- playbook.yaml - Ansible features (not currently used)
- inventory-test.yaml - Ansible features (not currently used)

## Connecting the dots:
Before you run vagrant up, make sure that you updated the Vagrantfile to your desired configuration.

Once you are done:
- _vagrant up_
  Wait for about 6 minutes to finish the build. Once done. You can try to ssh to your ansible-host vm. You can verify this by using "_vagrant status_"

- _vagrant ssh ansible-host_
  once you are login to your ansible-host vm, you can now verify if the other vm are reachable. The command to use is: "_ansible-playbook -i inventory playbook/ping.yml_"

## The Vagrantfile performs the following:
- Defines the VM's with static private IP addresses, vcpu, memory and vagrant-box
- Calls "bootstrap-node.sh" which puts IP/hostname information into /etc/hosts. This sets the host specific behavior of the playbook.
- Calls "playbook.yml" to config each box. (currently unused)
- Runs the Wazuh server "all-in-one" unattended install [script](https://documentation.wazuh.com/current/installation-guide/open-distro/all-in-one-deployment/unattended-installation.html)
- Outputs the results of the Wazuh server build script, including credential information to the user `vagrant` home directory
