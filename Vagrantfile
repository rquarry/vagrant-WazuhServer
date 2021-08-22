# -*- mode: ruby -*-
# vi: set ft=ruby :

$script = "curl -so ~/unattended-installation.sh https://packages.wazuh.com/resources/4.1/open-distro/unattended-installation/unattended-installation.sh && bash ~/unattended-installation.sh | tee creds.txt"

Vagrant.configure("2") do |config|
# Define VMs with static private IP addresses, vcpu, memory and vagrant-box.
  boxes = [
    {
      :name => "wazuh.server.com",
      :box => "ubuntu/focal64",
      :ram => 4096,
      :vcpu => 2,
      :ip => "192.168.29.2"
    }
  ]

  # Provision each of the VMs.
  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
#   Only Enable this if you are connecting to Proxy server
#      config.proxy.http     = "http://usernam:password@x.y:80"
#      config.proxy.https    = "http://usernam:password@x.y:80"
#      config.proxy.no_proxy = "localhost,127.0.0.1"
      config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
      config.ssh.insert_key = false
      config.vm.box = opts[:box]
      config.vm.hostname = opts[:name]
      config.vm.provider :virtualbox do |v|
        v.memory = opts[:ram]
        v.cpus = opts[:vcpu]
        # Initial boot of VM's hangs. Showing gui helps close to re-run vagrant file
        v.gui = true
      end
      config.vm.network :private_network, ip: opts[:ip]
      config.vm.provision :file do |file|
         file.source     = './keys/vagrant'
         file.destination    = '/tmp/vagrant'
        end
# Don't run ansible stuff from the forked repo
#      config.vm.provision :file do |file|
#        file.source     = './inventory-test.yaml'
#        file.destination    = '/home/vagrant/inventory-test.yaml'
#       end
      config.vm.provision :shell, path: "bootstrap-node.sh"
      config.vm.provision :shell, inline: $script
#      config.vm.provision :ansible do |ansible|
#        ansible.verbose = "v"
#        ansible.playbook = "playbook.yml"
      end
   end
  end
