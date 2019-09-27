# -*- mode: ruby -*-
# vi: set ft=ruby :

IMAGE_NAME = 'centos/7'

n_nodes = ENV['N_NODES'] || 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.box = IMAGE_NAME
    config.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 2
    end
      
    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "192.168.100.10"
        master.vm.hostname = "k8s-master"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "playbooks/setup-k8s-master.yml"
            ansible.inventory_path = "inventories"
            ansible.extra_vars = {
                node_ip: "192.168.100.10",
            }
        end
    end

    (1..n_nodes).each do |i|
        config.vm.define "k8s-node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.168.100.#{i + 20}"
            node.vm.hostname = "k8s-node-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "playbooks/setup-k8s-node.yml"
                ansible.inventory_path = "inventories"
                ansible.extra_vars = {
                    node_ip: "192.168.100.#{i + 20}",
                }
            end
        end
    end
end