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
