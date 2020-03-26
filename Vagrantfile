# -*- mode: ruby -*-
# vi: set ft=ruby :

# Inspired by: https://github.com/carmstrong/multinode-glusterfs-vagrant

glusterVolumes="glusterfs-0" # Single volume only at this time
servers = 2
serversIP = ''
client = 1
ipRange = "172.21.12."
glusterfs_version = "6.6"


Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.box_version = "20191114.0.0"

  # We setup three nodes to be gluster hosts, and one gluster client to mount the volume
  (1..servers).reverse_each do |i|
    ip = ipRange + "#{i+10}"
    serversIP = serversIP + "," + ip

    config.vm.define vm_name = "gluster-server-#{i}" do |config|
      config.cache.scope = :box
      config.vm.hostname = vm_name
      config.vm.network :private_network, ip: ip

      config.vm.provider :virtualbox do |v|
        v.customize ["modifyvm", :id, "--name", vm_name]
        v.customize ["modifyvm", :id, "--memory", "512"]
        v.customize ["modifyvm", :id, "--cpus", "1"]
      end

      config.vm.provision :shell, path: 'install-glusterfs', privileged: true, args: "#{glusterfs_version} server"

      if ( i == 1 )
        config.vm.provision :shell, path: 'setup-server', privileged: true, args: "#{serversIP} #{glusterVolumes.gsub(/\s+/, "")}"
      end
    end
  end

  if ( client == 1 )
    config.vm.define vm_name = "gluster-client" do |config|
      config.cache.scope = :box
      config.vm.hostname = vm_name
      ip = ipRange + "10"
      config.vm.network :private_network, ip: ip

      config.vm.provider :virtualbox do |v|
        v.customize ["modifyvm", :id, "--name", vm_name]
        v.customize ["modifyvm", :id, "--memory", "512"]
        v.customize ["modifyvm", :id, "--cpus", "1"]
      end
    
      config.vm.provision :shell, path: 'install-glusterfs', privileged: true, args: "#{glusterfs_version} client"
      config.vm.provision :shell, path: 'setup-client', privileged: true, args: "#{serversIP} #{glusterVolumes.gsub(/\s+/, "")}"
    end
  end
end

