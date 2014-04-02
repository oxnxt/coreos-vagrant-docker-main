# -*- mode: ruby -*-
# # vi: set ft=ruby :

require_relative 'override-plugin.rb'

INSTANCE = "docker-main"
IP = "172.12.8.121"
CLOUD_CONFIG_PATH = "./user-data.#{INSTANCE}"

Vagrant.configure("2") do |config|
  # Fix docker not being able to resolve private registry in VirtualBox
  config.vm.provider :virtualbox do |vb, override|
    puts "Provider is virtualbox"
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end

  config.vm.box = "coreos-alpha"
  config.vm.box_url = "http://storage.core-os.net/coreos/amd64-usr/alpha/coreos_production_vagrant.box"

  config.vm.provider :vmware_fusion do |vb, override|
    override.vm.box_url = "http://storage.core-os.net/coreos/amd64-usr/alpha/coreos_production_vagrant_vmware_fusion.box"
  end

  config.vm.define INSTANCE do |config|
    # Forwarded ports
    config.vm.network "forwarded_port", guest: 4243, host: 4243  # docker
    config.vm.network "forwarded_port", guest: 8000, host: 8000  # shipyard
  
    config.vm.hostname = INSTANCE
    config.vm.network :private_network, ip: IP
  
    # Enable NFS for sharing the host machine into the coreos-vagrant VM.
    config.vm.synced_folder "./#{INSTANCE}", "/home/core/share", id: "core", :nfs => true,  :mount_options   => ['nolock,vers=3,udp']
  
    if File.exist?(CLOUD_CONFIG_PATH)
      puts "Using #{CLOUD_CONFIG_PATH}"
      config.vm.provision :file, :source => "#{CLOUD_CONFIG_PATH}", :destination => "/tmp/user-data"
      config.vm.provision :shell, :inline => "mkdir -p /var/lib/coreos-vagrant && mv /tmp/user-data /var/lib/coreos-vagrant", :privileged => true
    end

  end

end
