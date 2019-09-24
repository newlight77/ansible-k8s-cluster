# Multi-nodes Kubernetes cluster

Kubernetes is an orchestration engine for docker containers in a cluster configuration. It is also known as `k8s` and it is open source and was developed initially by Google and then donated to “Cloud Native Computing foundation”. In Kubernetes setup we have one master node and multiple nodes. A cluster node is known as worker node or Minion. From the master node, we manage the cluster and its nodes using `kubeadm` and `kubect` command.

In this article, we will walk through steps to setup a multi node Kubernetes cluster for development purposes. Even though Minikube is an excellent option for getting started on a local machine, it's not as close as a multi-nodes cluster could be to a production environment. A multi-nodes Kubernetes cluster offer a production-like environment and gives the opportunity to work with realistic issues we could encounter when delivering softwares.

We will install a Kubernetes cluster on CentOS 7 / RHEL 7 with kubeadm utility. The cluster will consist of one master and two worker nodes. Ansible playbooks will be used for provisioning.

## Prerequisites

- [Vagrant](https://www.vagrantup.com/downloads.html)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- 3+ Machines running CentOS

## What will be installed ?

### Master

On the Master Node following components will be installed :

- API Server :

provides kubernetes API using Json / Yaml over http

- Scheduler :

performs the scheduling tasks like launching containers in worker nodes based on resource availability

- Controller Manager :

monitors replication controllers and create pods to maintain desired state.

- etcd :

stores configuration data of cluster and cluster state.

- Kubectl utility :

It is used by administrators to create pods, services... it connects to API Server on port 6443

### Worker

On Worker Nodes following components will be installed :

- Kubelet :

It is an agent which runs on every worker node, it connects to docker and takes care of creating, starting, deleting containers.

- Kube-Proxy :

It routes the traffic to appropriate containers based on ip address and port number of the incoming request.

- Pod :

Pod can be defined as a multi-tier or group of containers that are deployed on a single worker node or docker host.

## Step-by-step

We will go through many steps to setup the cluster, and while doing so we are automating everything as much as possible, and we also need to add testing ability.

**Step 1** : Bootstrap Ansible.

**Step 2** : Create a role for Kubelet installation.

**Step 2.1** : Install Docker and its dependent components.

**Step 2.2** : Kubelet will not start if the system has swap enabled, so we are disabling swap using the below code.

**Step 2.3** : Installing kubelet, kubeadm and kubectl using the below code.

### master

**Step 3** : Create a role for k8s master.

**Step 3.1** : Create a playbook

**Step 3.2** : install Kubelet

**Step 3.3** : Initialize the Kubernetes cluster with kubeadm using the below code

**Step 3.4** : Setup the kube config file for the vagrant user to access the Kubernetes cluster using the below code.

**Step 3.5** : Setup the container networking provider and the network policy engine using the below code.

**Step 3.6** : Generate kube join command for joining the node to the Kubernetes cluster and store the command in the file named join-command.

**Step 3.7** : Setup a handler for checking Docker daemon using the below code.

### nodes

**Step 4** : Create a role for k8s node.

**Step 4.1** : Create a playbook

**Step 4.2** : install Kubelet

**Step 4.3**: Join the nodes to the Kubernetes cluster using below code.

**Step 4.4** : Setup a handler for checking Docker daemon using the below code.

## Testing

With Vagrant, we ca create a virtual environment easily. This way we can test our Ansible playbook that we are going to build.

The following Vagrantfile is used to launch 3 Virtual machines :

- k8s-master : the Kubernetes master
- n workers : Kubernetes worker nodes

By default, 2 workder nodes are created. If you wish to have more nodes, you can set the variable envrionment as followed :

```sh
export N_NODES=3

vagrant up
```

`N_NODES` will be used in the Vagrantfile accordingly to launch workers VMs.

Upon completion of all the above steps, the Kubernetes cluster should be up and running. We can login to the master or worker nodes as follows:

```sh
$ ## Accessing master
$ vagrant ssh k8s-master
vagrant@k8s-master:~$ kubectl get nodes
NAME         STATUS   ROLES    AGE     VERSION
k8s-master   Ready    master   18m     v1.13.3
node-1       Ready    <none>   12m     v1.13.3
node-2       Ready    <none>   6m22s   v1.13.3
```

```sh
$ ## Accessing nodes
$ vagrant ssh node-1
$ vagrant ssh node-2
```
