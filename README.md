# Multi-nodes Kubernetes cluster

This blog post will walk you through the steps to setup a multi node Kubernetes cluster for development purposes. Even though Minikube is an excellent option for getting started on a local machine, it's not as close as a Multi node clusters could be to a production environment. A Multi-nodes Kubernetes cluster offer a production-like environment and gives the opportunity to work with realistic issues we could encounter when delivering softwares. 

We will setup a Kubernetes cluster that will consist of one master and two worker nodes. All the nodes will run Centos7 64-bit and Ansible playbooks will be used for provisioning.

## Prerequisites

- [Vagrant](https://www.vagrantup.com/downloads.html)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- 3+ Machines running CentOS

## Step-by-step

We will go through many steps to setup the cluster, and while doing so we are automating everything as much as possible, and we also need to add testing ability.

__Steps__ :

- Step 0 : Using Vagrant for testing
- Step 1 : Bootstraping an Ansible project
- Step 2 : Create a playbook to setup a K8s master
- Step 3 : Add a test for the K8s master playbook
- Step 4 : Create a playbook for the K8s node
- Step 5 : Add a test for the K8s node playbook
- Step 6 : Instantiate the K8s cluster

### Step 0 : Vagrantfile

With Vagrant, we ca create a virtual environment easily. This way we can test our Ansible playbook that we are going to build.

The following Vagrantfile is used to launch 3 Virtual machines :

- k8s-master : the Kubernetes master
- n workers : Kubernetes worker nodes

By default, 2 workder nodes are created. If you wish to have more nodes, you can set the variable envrionment as followed :

```sh
export N_NODES=3
```

`N_NODES` will be used in the Vagrantfile accordingly to launch workers VMs.

```vagrantfile
IMAGE_NAME = 'centos/7'
N = 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.box = IMAGE_NAME
    config.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 2
    end
      
    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "192.168.50.10"
        master.vm.hostname = "k8s-master"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.100.10",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.100.#{i + 10}",
                }
            end
        end
    end
end
```
