# -*- mode: ruby -*-
# vi: set ft=ruby :

$script = <<SCRIPT
curl -fsSL get.docker.com -o get-docker.sh && sh get-docker.sh
sudo apt-get install -y nfs-common
sudo mkdir -p /d/shared
sudo mount -t nfs -o proto=tcp,port=2049 192.168.33.10:/ /d/shared
SCRIPT

$nfs = <<SCRIPT
sudo apt-get update
sudo apt-get install -y nfs-kernel-server nfs-common
sudo mkdir -p /d/shared
sudo echo '/d/shared 192.168.33.9(rw,no_root_squash,fsid=0,insecure,no_subtree_check,sync)' >> /etc/exports
sudo echo '/d/shared 192.168.33.11(rw,no_root_squash,fsid=0,insecure,no_subtree_check,sync)' >> /etc/exports
sudo echo '/d/shared 192.168.33.12(rw,no_root_squash,fsid=0,insecure,no_subtree_check,sync)' >> /etc/exports
sudo service nfs-kernel-server restart
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.define "nfs" do |nfs|
    nfs.vm.host_name = "nfs"
    nfs.vm.network :private_network, ip: "192.168.33.10"
    nfs.vm.provision "shell", inline: $nfs
  end

  config.vm.define "master" do |master|
    master.vm.host_name = "master"
    master.vm.network :private_network, ip: "192.168.33.9"
    master.vm.provision "shell", inline: $script
  end

  config.vm.define "a1" do |a1|
    a1.vm.host_name = "a1"
    a1.vm.network :private_network, ip: "192.168.33.11"
    a1.vm.provision "shell", inline: $script
  end

  config.vm.define "a2" do |a2|
    a2.vm.host_name = "a2"
    a2.vm.network :private_network, ip: "192.168.33.12"
    a2.vm.provision "shell", inline: $script
  end
end
