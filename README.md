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



---

## 3. Infrastructure Setup with Terraform

1. Clone the Terraform repo 
   ```bash
   git clone <terraform-infra-repo>
   cd terraform-infra-repo
# aws-k8s-ha-cluster-terraform-monitoring
