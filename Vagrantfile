# -*- mode: ruby -*-
# vi: set ft=ruby :

# Default Fuel master settings
bifrost_memory = 4096
bifrost_cpu = 1

# Default openstack server settings.
openstack_server_memory = 2048
openstack_server_disk_size = '100G'

#udp port numbers to connect server1/2 with back to back links
server1_to_server2  = [9988, 8899]

## When running `vagrant up` run it with the `--no-parallel` option.
## This ensures that the fuel_master comes up first

Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.

  # Disable the synced_folder feature
  config.vm.synced_folder '.', '/vagrant', :disabled => true

  config.vm.define :bifrost do |node|
    node.vm.provider :libvirt do |domain|
      domain.memory = bifrost_memory
      domain.cpus = bifrost_cpu
    end

    node.vm.box = "xenial64"
    node.vm.network :private_network,
      :ip => '10.20.0.2',
      :netmask => '255.255.255.0',
      :libvirt__forward_mode => 'none',
      :libvirt__network_name => 'pxe_network',
      :libvirt__dhcp_enabled => false
    # During provisioning of docker containers, Fuel will attempt to bring up
    # the fuel menu but fail, due to ssh settings on the vagrant provisioner. its okay
    # its not a problem. Just ignore.
  end

  # 1st Openstack Server
  config.vm.define :openstack_node1 do |node|
    node.vm.communicator = ''
    node.vm.provider :libvirt do |domain|
      domain.storage :file, :size => openstack_server_disk_size, :type => 'qcow2'
      domain.boot 'network'
      domain.boot 'hd'
      domain.memory = openstack_server_memory
      domain.management_network_name = 'pxe_network'
      domain.management_network_address = '10.20.0.0/24'
      domain.management_network_mac = '361122334411'
    end
    # Back to back connection between openstack server 1 and 2
    node.vm.network :private_network,
      :libvirt__tunnel_type => 'udp',
      :libvirt__tunnel_port => server1_to_server2[0],
      :libvirt__tunnel_local_port => server1_to_server2[1]
  end

  # 2nd Openstack Server
  config.vm.define :openstack_node2 do |node|
    node.vm.provider :libvirt do |domain|
      domain.storage :file, :size => openstack_server_disk_size , :type => 'qcow2'
      domain.boot 'network'
      domain.boot 'hd'
      domain.memory = openstack_server_memory
      domain.management_network_name = 'pxe_network'
      domain.management_network_address = '10.20.0.0/24'
      domain.management_network_mac = '361122334455' # in private mac range
    end
    # Back to back connection between openstack server 1 and 2
    node.vm.network :private_network,
      :libvirt__tunnel_type => 'udp',
      :libvirt__tunnel_port => server1_to_server2[1],
      :libvirt__tunnel_local_port => server1_to_server2[0]
  end
end
