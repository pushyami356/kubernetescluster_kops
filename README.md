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

# 📌 1. Prerequisites

Before creating the cluster, install:

AWS CLI
kubectl
Kops
SSH Key Pair
Route53 Hosted Zone
AWS Account with required permissions

# AWS CLI
snap install aws-cli --classic

# Verify Installed Tools
  Check AWS CLI installation:
  aws --version

 # Aws configure

# SSH Key Pair
  Generate ssh keys:
  ssh-keygen
  
# Install kops
curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops
sudo mv kops /usr/local/bin/kops

# Install and Set Up kubectl on Linux:

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Create an S3 Bucket
aws s3 mb s3://<your-kops-state-bucket>

# Enable versioning:

aws s3api put-bucket-versioning \
--bucket <your-kops-state-bucket> \
--versioning-configuration Status=Enabled

Export the state store:

export KOPS_STATE_STORE=s3://<your-kops-state-bucket>

# Create the Cluster

kops create cluster --name=<cluster-name> --state=s3://<your-kops-state-bucket> --zones=us-east-1a,us-east-1b --node-count=2 \
--node-size=t3.small --control-plane-size=t3.medium --dns-zone=<your-domain>
Review the configuration:

kops edit cluster <cluster-name>

# Update the Cluster
kops update cluster --name=kubevpro.k8s.local --state=s3://kopsstatebkt2043 --yes --admin 

# Validate the Cluster
kops validate cluster --name=kubevpro.k8s.local --state=s3://kopsstatebkt2043

# Delete Cluster
kops delete cluster --name=kubevpro.k8s.local --state=s3://kopsstatebkt2043 --yes


# Route53 Hosted Zone
AWS Account with required permissions
