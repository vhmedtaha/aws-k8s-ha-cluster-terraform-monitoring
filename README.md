# AWS Kubernetes HA Cluster Setup with Terraform and Monitoring

This repository documents the steps to deploy a **High Availability (HA) Kubernetes cluster** on AWS using **Terraform**, with **Calico** as the CNI plugin and **Prometheus + Grafana** for monitoring.

---

## 1. Prerequisites

- AWS account with proper IAM permissions
- Terraform installed on your local machine
- containerd (on EC2 nodes)
- kubectl kubeadm, kubelet
- Helm installed for deploying Prometheus and Grafana
- Basic knowledge of AWS networking (VPC, Subnets, Security Groups)

---

## 2. Infrastructure Architecture

<img width="1161" height="581" alt="Image" src="https://github.com/user-attachments/assets/34bc2783-38ac-4dd6-8694-06e24d5d83ff" />


This infrastructure is deployed using Terraform and provides a **secure, highly available (HA), and scalable** foundation for running a Kubernetes cluster on AWS.


## 🌟 Architecture Overview

The design is built on the principle of **Defense-in-Depth** and **High Availability** across three Availability Zones (AZs) in the `us-east-1` region.

---

## 3. Infrastructure Setup with Terraform

1. Clone this repo to get Terraform files 
   ```bash
   git clone https://github.com/vhmedtaha/aws-k8s-ha-cluster-terraform-monitoring.git
2. After clone the repo Applay terraform files to pull up your Infrastructure

---

## 4. Access EC2 Instances

1. Use SSH to connect to all master and worker nodes
   ```bash
   ssh -i <keypair>.pem ubuntu@<node-ip>
---

## 5. This configration will be in all nodes ( masters + worker )

1. Install Kubernetes Tools
   
    ```bash
   sudo mkdir -p /etc/apt/keyrings
   curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
   echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
3. Update package index
   
   ```bash
   sudo apt-get update
4. Install Kubernetes components
   
   ```bash
     sudo apt-get install -y kubelet kubeadm kubectl
5. Enable and start kubelet service
   
   ```bash
     sudo systemctl enable --now kubelet
6. Install Container Runtime ( Containerd )
   
   ```bash
     sudo apt-get update
     sudo apt-get install -y ca-certificates curl gnupg lsb-release

   # Add Docker’s official GPG key
     sudo mkdir -p /etc/apt/keyrings
     curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

   # Set up the Docker repository
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list >   /dev/null

   # Install containerd
    sudo apt-get update
    sudo apt-get install -y containerd.io

   # Configure and start containerd
    sudo mkdir -p /etc/containerd
    sudo containerd config default | sudo tee /etc/containerd/config.toml
    sudo systemctl restart containerd
    sudo systemctl enable containerd
   
   # Configure Kernel & Sysctl for Kubernetes Networking
   sudo modprobe overlay
   sudo modprobe br_netfilter
   sudo tee /etc/sysctl.d/k8s.conf <<EOF
   net.bridge.bridge-nf-call-iptables  = 1
   net.ipv4.ip_forward                 = 1
   net.bridge.bridge-nf-call-ip6tables = 1
   EOF
   sudo sysctl --system
7. Initialize the control plane (Choose one of masters to take the Lead)
   
   ```bash
     sudo kubeadm init --control-plane-endpoint "LOAD_BALANCER_DNS:LOAD_BALANCER_PORT" --upload-certs
* Put your load balancer Dns  & 6443 Port

7. The output looks similar to:
   
   ```bash
     You can now join any number of control-plane node by running the following command on each as a root:
    kubeadm join 192.168.0.200:6443 --token 9vr73a.a8uxyaju799qwdjv --discovery-token-ca-cert-hash sha256:7c2e69131a36ae2a042a339b33381c6d0d43887e2de83720eff5359e26aec866 --control-plane --certificate-key   f8902e114ef118304e561c3ecd4d0b543adc226b7a07f675f56564185ffe0c07

   Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
   As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use kubeadm init phase upload-certs to reload certs afterward.

   Then you can join any number of worker nodes by running the following on each as root:
    kubeadm join 192.168.0.200:6443 --token 9vr73a.a8uxyaju799qwdjv --discovery-token-ca-cert-hash sha256:7c2e69131a36ae2a042a339b33381c6d0d43887e2de83720eff5359e26aec866
* Take this output to join masters and worker nodes

8. Choose Your Network solution ( I Choosed Calico )
   ```bash
     kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

9. Install HELM
  ```bash
    curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

    helm version
   



   
    


   
