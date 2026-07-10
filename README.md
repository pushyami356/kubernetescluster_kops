#  kubernetescluster_kops
This repository demonstrates how to create and manage a production-ready Kubernetes cluster on AWS using Kops. It includes cluster creation, validation, application deployment, networking, storage, services, and cleanup.

## Architecture

AWS Components

VPC
Public and Private Subnets
Internet Gateway
Route Tables
EC2 Instances
IAM Roles
Route 53 Hosted Zone
S3 Bucket (Kops State Store)

## Kubernetes Components

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

 - AWS CLI
 - kubectl
 - Kops
 - SSH Key Pair
 - Route53 Hosted Zone
 - AWS Account with required permissions

## AWS CLI
#### Install aws cli

  ```bash
snap install aws-cli --classic
```
### Verify Installed Tools
  Check AWS CLI installation:

  ```bash
aws --version
```
  
### AWS Configuration Setup
Log in to the AWS Management Console in your browser and complete the following steps:
#### Step 1: Create IAM User
- Go to IAM (Identity and Access Management)
   - Create a new user
   - Attach the policy: AdministratorAccess
   - Create the user
#### Step 2: Create Access Keys
- Click on the created IAM user
- Go to Security Credentials
- Click Create Access Key
- Select Command Line Interface (CLI)
- Confirm and proceed
- Create the access key
- Copy or download the Access Key ID and Secret Access Key securely
  
**Now configure AWS:**
```bash
aws configure
```

**Enter the following details:**
```bash
AWS Access Key ID [****************7XV7]: 
AWS Secret Access Key [****************HpDr]: 
Default region name [us-east-1]: us-east-1
Default output format [json]: json
```

### SSH Key Pair
  - Generate ssh keys:
  ```bash
  ssh-keygen
  ls .ssh
```
  
### Install kops
  ```bash
curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops
sudo mv kops /usr/local/bin/kops
```

### Install and Set Up kubectl on Linux:
  ```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### Create an S3 Bucket
  ```bash
    aws s3api create-bucket --bucket kopsstatebkt2043 --region us-east-1
```

### Enable versioning:
  ```bash
aws s3api put-bucket-versioning --bucket <your-kops-state-bucket> --versioning-configuration Status=Enabled
```

Export the state store:
  ```bash
 export KOPS_STATE_STORE=s3://<your-kops-state-bucket>
```

### Create the Cluster
  ```bash
kops create cluster --name=<cluster-name> --state=s3://<your-kops-state-bucket> --zones=us-east-1a,us-east-1b --node-count=2 \
--node-size=t3.small --control-plane-size=t3.medium --dns-zone=<your-domain>
```
Review the configuration:
  ```bash
kops edit cluster <cluster-name>
```
### Update the Cluster
  ```bash
kops update cluster --name=kubevpro.k8s.local --state=s3://kopsstatebkt2043 --yes --admin
```

### Validate the Cluster

  ```bash
kops validate cluster --name=kubevpro.k8s.local --state=s3://kopsstatebkt2043
```

### Delete Cluster

```bash
kops delete cluster --name=kubevpro.k8s.local --state=s3://kopsstatebkt2043 --yes
```

## Create a Hosted Zone in R53: 
  - Navigate to Route53 and create a public hosted zone.
  - The hosted zone creates 2 records – NS record and SOA record. 
  - The NS record has four name servers, copy all the name servers and create records in GoDaddy
## Create NS records in Domain Registrar (GoDaddy): 

Login to your GoDaddy account and create 4 NS records for the name servers of the AWS Hosted Zone. 

### Create an Ingress Controller: 

### Create an ingress controller with command 
  ```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.3/deploy/static/provider/aws/deploy.yaml
```
### Create an EBS volume for DB deployment:

AWS Account with required permissions
