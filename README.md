# Kubernetes Cluster on GCP with kubeadm

Deploy a Kubernetes testing cluster on GCP using kubeadm.

## Prerequisites

You need to have the `gcloud` CLI tool installed and configured.

## Setup Instructions

### 1. Create a GCP Project

Create a GCP project using the `gcloud` CLI tool.

```bash
gcloud projects create gpeitzner-gcp-k8s-dev
gcloud config set project gpeitzner-gcp-k8s-dev
```

### 2. Create VPC and Subnet

Create a VPC and subnet for your cluster.

```bash
gcloud compute networks create k8s-vpc \
  --subnet-mode=custom \
  --bgp-routing-mode=regional

gcloud compute networks subnets create k8s-subnet \
  --network=k8s-vpc \
  --range=10.0.0.0/24 \
  --region=us-central1
```

### 3. Create Firewall Rules

Create firewall rules to allow SSH and internal cluster communication.

```bash
gcloud compute firewall-rules create k8s-allow-ssh \
  --network=k8s-vpc \
  --allow=tcp:22 \
  --source-ranges=0.0.0.0/0

gcloud compute firewall-rules create k8s-allow-internal \
  --network=k8s-vpc \
  --allow=tcp,udp,icmp
```

### 4. Create Compute Engine VMs

Create one control plane node and two worker nodes.

```bash
gcloud compute instances create k8s-control-plane \
  --machine-type=e2-medium \
  --subnet=k8s-subnet \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud \
  --zone=us-central1-a

gcloud compute instances create k8s-worker-1 k8s-worker-2 \
  --machine-type=e2-medium \
  --subnet=k8s-subnet \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud \
  --zone=us-central1-a
```

### 5. SSH into the VMs

Connect to each VM using the `gcloud` CLI.

```bash
gcloud compute ssh k8s-control-plane --zone=us-central1-a
gcloud compute ssh k8s-worker-1 --zone=us-central1-a
gcloud compute ssh k8s-worker-2 --zone=us-central1-a
```

### 6. Install container runtime and kubeadm

Run the following on all nodes.

```bash

```

### 7. Initialize the control plane

Run on the control plane node.

```bash

```

### 8. Join the worker nodes

Run this on the control plane to get the join command:

```bash
kubeadm token create --print-join-command
```

Run the printed command on each worker node.

### 9. Delete all resources

Delete the VMs, firewall rules, subnet, and VPC. Optionally delete the project.

```bash
gcloud compute instances delete k8s-control-plane k8s-worker-1 k8s-worker-2 \
  --zone=us-central1-a

gcloud compute firewall-rules delete k8s-allow-ssh k8s-allow-internal

gcloud compute networks subnets delete k8s-subnet \
  --region=us-central1

gcloud compute networks delete k8s-vpc

# Optional: delete the project (this removes everything in it)
gcloud projects delete gpeitzner-gcp-k8s-dev
```
