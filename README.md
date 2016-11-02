# Vagrant-bifrost

This is a test of bifrost using libvirt-vagrant.

## Topology
[ SVG ]

## Requirements

* Linux OS
* Vagrant version 1.8.6+
* Libvirt 1.3.1+
* Packer

## Create Xenial Vagrant Image

```
git clone https://github.com/chef/bento
cd bento
packer build -only qemu  ubuntu-16.04-amd64.json
vagrant box add xenial64 builds/ubuntu_build.box
```

## Clone vagrant-bifrost and start the VMs.

```
git clone https://github.com/linuxsimba/vagrant-bifrost
cd vagrant-bifrost
vagrant up --no-parallel
```
