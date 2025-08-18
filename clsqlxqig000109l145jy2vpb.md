---
title: "Create and Manage a Kubernetes Cluster from Scratch using AWS EC2 instances"
seoTitle: "Create and manage Kubernetes cluster from scratch"
seoDescription: "Create and manage Kubernetes cluster from scratch"
datePublished: Sat Feb 17 2024 21:45:17 GMT+0000 (Coordinated Universal Time)
cuid: clsqlxqig000109l145jy2vpb
slug: create-and-manage-a-kubernetes-cluster-from-scratch-using-amazon-ec2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1708206805466/28e9c658-ef95-4b68-8152-dfdd71b2f73a.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1708205002604/d264fa67-c5d5-4da3-8595-5e2fe37983d9.png
tags: aws, kubernetes, cloud-computing, orchestration, k8scluster, ec2-instance

---

Kubernetes has become the go-to solution for container orchestration, offering robust capabilities to automate the deployment, scaling, and management of containerized applications. In this blog post, we will walk through the process of creating a Kubernetes cluster that adheres to best practices from scratch using the `kubeadm` cluster bootstrapping utility. Even if you intend to use fully managed Kubernetes clusters, this tutorial provides you with a deeper understanding of Kubernetes clusters and helps you decide on the cluster configuration that is best for your requirements.

### Blog post Objectives

Upon completion of this tutorial, you will be able to:

* Install kubeadm and its dependencies
    
* Initialize a control-plane  node
    
* Join a node to a cluster
    
* Configure a network plugin
    

### Prerequisites

You should be familiar with:

* Working with Kubernetes to deploy applications
    
* Working at the command line in Linux
    

At the end of this blog post, you will be able to create a K8S Cluster with a Master Node and two Worker Nodes

![environment after](https://cloudacademy.com/_next/hanami/image/?url=https%3A%2F%2Fassets.cloudacademy.com%2Fbakery%2Fmedia%2Fuploads%2Flaboratories%2Fenvironment_end_images%2Fafter_Bh4Lxw9.png&w=3840&q=75 align="center")

This tutorial experience involves Amazon Web Services (AWS), and we'll use the AWS Management Console.

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20220203152252-1-c91e8064-669f-4444-b3ba-c0c39199176a.png align="center")

<details data-node-type="hn-details-summary"><summary>The AWS Management Console is a web control panel for managing all your AWS resources, from EC2 instances to SNS topics. The console enables cloud management for all aspects of the AWS account, including managing security credentials and even setting up new IAM Users.</summary><div data-type="detailsContent"></div></details>

## Creating 3 AWS EC2 Instances

Start by creating 3 Ubuntu instances with the following names:

1. instance-a
    
2. instance-b
    
3. instance-c
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1708198538065/ca375544-ff7c-49a1-971d-69e6d68d492e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1708199547074/08afac0d-af3f-41e8-b795-4557cc54795e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1708198758275/af4716e8-ad77-4628-a909-64b5e0c06399.png align="center")

## Connecting to an EC2 Instance

We will connect to the three Amazon EC2 instances and configure them to be a Kubernetes cluster. In later steps, we'll connect to instances named **instance-b** and **instance-c**.

Now we have our 3 EC2 instances UP and running, let's connect to the instance named **instance-a** using the Amazon EC2 Instance Connect:

Right-click the instance named **instance-a**, and click **Connect**:

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20220406121346-6-0126ad09-c02c-42d7-a329-73b67746ba72.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1708199309616/2b7bf465-41e9-4b75-bceb-7ea5233b4d6e.png align="center")

A browser-based shell will open in a new window and you will see a shell similar to:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1708199807449/c3aa2fca-cf35-4db5-a4be-d5f6dfe32fa1.png align="center")

Now that we are connected to our **instance-a** let's see what ***kubeadm*** is:

`kubeadm` is a tool that allows you to easily create Kubernetes clusters. It can also perform a variety of cluster lifecycle functions, such as upgrading and downgrading the version of Kubernetes on nodes in the cluster. We will use `kubeadm` to create a Kubernetes cluster from scratch. Creating clusters with `kubeadm` is the recommended way for learning Kubernetes, creating small clusters, and as a piece of more complex systems for more enterprise-ready clusters.

## Installing Kubeadm and its Dependencies

Our three EC2 instances running the Ubuntu distribution of Linux. we will configure the instance "**instance-a"** as a Kubernetes control-plane and the other instances as worker nodes in the cluster. In this step, we will install `kubeadm` and its dependencies, including containerd.

1. Enter the following command to update the system's apt package manager index and update packages required to install containerd:
    

```bash

# Update the package index
sudo apt-get update
# Update packages required for HTTPS package repository access
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common gnupg lsb-release
```

1. Allow forwarding IPv4 by loading the br\_netfilter module with the following commands:
    

```bash

# Load br_netfilter module
sudo modprobe overlay
sudo modprobe br_netfilter
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```

1. Allow the Linux node's iptables to correctly view bridged traffic with the following commands:
    

```bash

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
# Apply sysctl params without reboot
sudo sysctl --system
```

1. Install containerd using the DEB package distributed by Docker with the following commands:
    

```bash

# Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
# Set up the repository
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
# Install containerd
sudo apt-get update
sudo apt-get install -y containerd.io=1.6.18-1
```

*Note*: This is only one way of installing containerd. Please refer [here](https://github.com/containerd/containerd/blob/main/docs/getting-started.md) for the other options.

1. Configure the systemd cgroup driver with the following commands:
    

```bash

# Configure the systemd cgroup driver
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
sudo systemctl restart containerd
```

This is required to mitigate the instability of having two cgroup managers. Please refer [here](https://kubernetes.io/docs/setup/production-environment/container-runtimes/) for further explanation.

1. Install `kubeadm`, `kubectl`, and `kubelet` from the official Kubernetes package repository:
    

```bash

# Add the Google Cloud packages GPG key
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
# Add the Kubernetes release repository
sudo add-apt-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
# Update the package index to include the Kubernetes repository
sudo apt-get update
# Install the packages
sudo apt-get install -y kubeadm=1.28.1-00 kubelet=1.28.1-00 kubectl=1.28.1-00
```

1. Prevent automatic updates to the installed packages with the following command:
    

```bash

sudo apt-mark hold kubelet kubeadm kubectl
```

The version of all the packages is set to 1.28.1 for consistency in this tutorial experience, and so that we can perform a cluster upgrade in a later blog post.

1. Display the help page for kubeadm:
    

```bash

kubeadm
```

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20221104092617-38-80ae7227-aabb-42ef-a48f-cd110a57ee44.png align="center")

Read through the output to get a high-level overview of how a cluster is created and the commands that are available in `kubeadm`.

After setting the first instance-a up we must do the same thing for instance-b and instance-c: Right-click the instance named **instance-b**, and click **Connect**:

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20220406121346-6-0126ad09-c02c-42d7-a329-73b67746ba72.png align="center")

After connecting the instance-b, enter the same commands we have executed before for the instance-b, The same steps should be done for instance-c

Now that `kubeadm` is configured on the 3 EC2 instances, connect back the instance-a to configure it as the master node for our k8s cluster.

Initializing the Kubernetes Master Node

We will use `kubeadm` to initialize the control-plane node. The initialization process will create a certificate authority for secure cluster communication and authentication, and start all the node components (`kubelet`), control-plane components (API server, controller manager, scheduler, etcd), and common add-ons (`kube-proxy`, DNS). You will see how easy the initialization process is with `kubeadm`.

The initialization uses sensible default values that adhere to best practices. However, [many command options](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/#options) are available to configure the process, including if you want to provide your own certificate authority or if you want to use an external etcd key-value store. One option that you will use is required by the pod network plugin that you will install after the control-plane is initialized. `kubeadm` does not install a default network plugin for you. You will use Calico as the pod network plugin. Calico supports Kubernetes network policies. For network policies to function properly, you must use the `--pod-network-cidr` option to specify a range of IP addresses for the pod network when initializing the control-plane node with `kubeadm`.

There are many network plugins besides Calico. Calico is used primarily because it is used in clusters in [Kubernetes certification exams](https://docs.linuxfoundation.org/tc-docs/certification/tips-cka-and-ckad) and it supports network policies. Calico is used internally by AWS, Azure, and GCP for their managed Kubernetes offerings, so you can be certain it is production-ready. However, if all of your environments live in AWS, you may consider the [Amazon VPC network plugin](https://github.com/aws/amazon-vpc-cni-k8s).

1. Initialize the control-plane node using the init command:
    

```bash

sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --kubernetes-version=stable-1.28
```

The pod network CIDR block (`192.168.0.0/16`) is the default used by [Calico.](https://cloudacademy.com/lab/create-manage-kubernetes-cluster-scratch/initializing-kuberenetes-master-node/?context_id=7846&context_resource=lp&program=b145d902-677b-49a8-ba1d-371c35e8543a#) The CIDR does not overlap with the Amazon VPC network CIDR used in this tutorial. If it did, you would need to perform additional configuration of Calico later on to avoid the overlap. The output reports the steps that `kubeadm` takes to initialize the control-plane:

![alt](https://assets.cloudacademy.com/bakery/media/uploads/blobid0-f7a28574-377b-43ff-b13c-fcbe2d8111f9.png align="center")

Read through the output to understand what is happening. At the end of the output, useful commands for configuring `kubectl` and joining worker nodes to the cluster are given:

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20221102131801-6-b248c8ed-dbd3-45e6-a7b7-d0f309654427.png align="center")

1. Copy the `kubeadm join` command at the end of the output and store it somewhere you can access later.
    

It is simply convenient to reuse the given command, although you can regenerate it and create new tokens using the `kubeadm token` command. The join tokens expire after 24 hours by default.

1. Initialize your user's default `kubectl` configuration using the admin kubeconfig file generated by `kubeadm`:
    

```bash

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

1. Confirm you can use `kubectl` to get the cluster component statuses:
    

```bash

kubectl get componentstatuses
```

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20221102131931-7-f4bc9caa-51a9-403d-8aec-58e6d5a91f77.png align="center")

The output confirms that the **scheduler**, **controller-manager**, and **etcd** are all **Healthy**. The Kubernetes API server is also operational, or `kubectl` would have returned an error attempting to connect to the API server. Enter `kubeadm token --help` if you would like to know more about `kubeadm` tokens.

1. Get the nodes in the cluster:
    
    ```bash
    
    kubectl get nodes
    ```
    
    ![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20221102132008-8-8067fb92-90ca-442f-ac92-3d7b5322dce7.png align="left")
    
    The control-plane node is reporting a **STATUS** of **NotReady**. Notice `kubeadm` gives the node a **NAME** based on its IP address. The `--node-name` option can be used to override the default behavior.
    
2. Describe the node to probe deeper into its NotReady status:
    

```bash

kubectl describe nodes
```

In the **Conditions** section of the output, observe the **Ready** condition is False, and read the **Message**:

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20221103163900-36-b8def2ac-3de4-460c-a466-2dc2ede8cd57.png align="left")

The kubelet is not ready because the network plugin is not ready. The **cni config uninitialized** refers to the [container network interface](https://github.com/containernetworking/cni) (CNI) and is a related problem. Network plugins implement the CNI interface. You will resolve the issue by initializing the Calico network plugin.

1. Enter the following commands to create the Calico network plugin for pod networking:
    

```bash

kubectl apply -f https://clouda-labs-assets.s3.us-west-2.amazonaws.com/k8s-common/1.28/scripts/calico.yaml
```

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20230210150701-1-fc5fb563-bf3a-4c4b-9914-b1a193762131.png align="center")

A variety of resources are created to support pod networking. A daemonset is used to run a Calico-node pod on each node in the cluster. The resources include several custom resources (customresourcedefinition) that extend the Kubernetes API, for example, to support network policies (networkpolicies.crd.projectcalico.org). Many network plugins have a similar installation procedure.

1. Watch the status of the nodes in the cluster:
    

```bash

watch kubectl get nodes
```

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20221102223750-13-f98f69e4-b3d5-426b-8600-3160ec52082e.png align="left")

With the network plugin initialized, the control-plane node is now **Ready**. *Note*: It may take a minute to reach the **Ready** state.

Press *ctrl+c* to stop watching the nodes.

## Joining a Worker Node to the Kubernetes Cluster

The process of adding a worker node with `kubeadm` is even simpler than initializing a control-plane node. You will join a worker node to the cluster using the command that `kubeadm init`

1. Open a second terminal connected to **instance-b** that is listed in the EC2 Console.
    

Refer back to the earlier steps on connecting to instance-a using EC2 Instance Connect, if required.

1. Enter `sudo` followed by the `kubeadm join` command that you stored from the output of `kubeadm init`. It resembles:
    

```bash

sudo kubeadm join 10.0.0.100:6443 --token ... --discovery-token-ca-cert-hash sha256:...
```

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20221103102138-28-d881d942-12be-4b6e-b4c0-df46b189707b.png align="center")

Read through the output to understand the operations `kubeadm` performed.

1. In the control-plane node's SSH shell, confirm the worker node is part of the cluster:
    

```bash

kubectl get nodes
```

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20221103095310-15-7c1a6850-f4a7-4c03-84c1-50cf81df6b63.png align="left")

The worker node appears with a role of **&lt;none&gt;**.

*Note*: It may take a minute for the worker node to become **Ready**.

Confirm that all the pods in the cluster are running:

```bash

kubectl get pods --all-namespaces
```

![alt](https://assets.cloudacademy.com/bakery/media/uploads/content_engine/image-20230210150817-3-44e252f3-6007-4536-97a2-1458a16f3544.png align="left")

All of the pods are **Running**, and the two-node cluster is operational. Notice that there are two Calico pods that support pod networking on each node.

### Summary

In this blog post, we have transformed 3 EC2 simple instances into a Kubernetes Cluster having one Master Node and 2 Worker Nodes, we have also configured Calico as a pod network plugin.