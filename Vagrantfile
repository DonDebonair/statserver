# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.define "statserver" do |statserver|
    # statserver.vm.box = "centos-6-4"
    # statserver.vm.box_url = "https://dl.dropboxusercontent.com/s/z85s74gv74m6flo/centos-6-4.box"
    # statserver.vm.box = "wheezy64"
    # statserver.vm.box_url = "https://dl.dropboxusercontent.com/s/j887m9989t2g8zj/wheezy64.box"
    statserver.vm.box = "precise32"
    statserver.vm.box_url = "http://files.vagrantup.com/precise32.box"
    statserver.vm.network :private_network, ip: "192.168.33.30"

    statserver.vm.provision "ansible" do |ansible| 
      ansible.playbook = "statserver.yml"
    end 
  end

  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--memory", "2048"]
  end

end
