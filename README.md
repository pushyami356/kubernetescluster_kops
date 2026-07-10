# kubernetescluster_kops
This repository demonstrates how to create and manage a production-ready Kubernetes cluster on AWS using Kops. It includes cluster creation, validation, application deployment, networking, storage, services, and cleanup.

# Architecture

AWS Components

VPC
Public and Private Subnets
Internet Gateway
Route Tables
EC2 Instances
IAM Roles
Route 53 Hosted Zone
S3 Bucket (Kops State Store)

# Kubernetes Components

Control Plane
Worker Nodes
Namespaces
Deployments
Services
Persistent Volumes (PV)
Persistent Volume Claims (PVC)
Storage Classes
Jobs
CronJobs
Ingress

# Prerequisites

Before creating the cluster, install:

AWS CLI
kubectl
Kops
SSH Key Pair
Route53 Hosted Zone
AWS Account with required permissions

# Create an S3 Bucket
aws s3 mb s3://<your-kops-state-bucket>

Enable versioning:

aws s3api put-bucket-versioning \
--bucket <your-kops-state-bucket> \
--versioning-configuration Status=Enabled

Export the state store:

export KOPS_STATE_STORE=s3://<your-kops-state-bucket>

# Create the Cluster

kops create cluster --name=<cluster-name> --state=s3://<your-kops-state-bucket> --zones=us-east-1a,us-east-1b --node-count=2 \
--node-size=t3.small --control-plane-size=t3.medium --dns-zone=<your-domain>

# AWS CLI
snap install aws-cli --classic

# kubectl

Install and Set Up kubectl on Linux:

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Kops
kops create cluster --name=kubevpro.k8s.local --state=s3://kopsstatebkt2043 --zones=us-east-1a,us-east-1b --node-count=2 --node-size=t3.small --control-plane-size=c7i-flex.large --node-volume-size=12 --control-plane-volume-size=12 --ssh-public-key ~/.ssh/id_ed25519.pub

kops update cluster --name=kubevpro.k8s.local --state=s3://kopsstatebkt2043 --yes --admin 

kops validate cluster --name=kubevpro.k8s.local --state=s3://kopsstatebkt2043

# SSH Key Pair
  Generate ssh keys:
  ssh-keygen
  
# Route53 Hosted Zone
AWS Account with required permissions
