🚀 Three-Tier Web Application on AWS EKS  

🧩 Overview  

This project demonstrates the deployment of a **Three-Tier Web Application** (ReactJS frontend, NodeJS backend, and MongoDB database) on **Amazon EKS (Elastic Kubernetes Service)**.  
It includes complete setup steps — from IAM configuration to deploying the app, integrating monitoring tools, and using AWS Load Balancer with Kubernetes.  


⚙️ Prerequisites  

Before starting, ensure you have:  
- ✅ Basic knowledge of **Docker**, **Kubernetes**, and **AWS**  
- ✅ An **AWS Account** with required permissions  
- ✅ Access to an **EC2 Instance** (Ubuntu recommended)  

📁 Project Structure  

| Directory | Description |
|------------|-------------|
| **Application-Code/** | Contains the complete ReactJS + NodeJS source code |
| **Kubernetes-Manifests-Files/** | Contains all Kubernetes YAML manifests for deployment |
| **buildspec.yml / Dockerfile** | Used for CI/CD integration and containerization |

🛠️ Tools & Technologies  

| Category | Tools |
|-----------|--------|
| **Cloud & Infrastructure** | AWS CLI, EC2, EKS, IAM |
| **Containerization** | Docker, Docker Compose |
| **Orchestration** | Kubernetes (kubectl, eksctl) |
| **Monitoring** | Helm, Prometheus, Grafana |
| **CI/CD** | GitHub Actions / AWS CodePipeline |

 🌐 Architecture Overview  

A simplified three-tier setup:  
- **Frontend:** ReactJS served via Node.js  
- **Backend:** Express.js API connected to MongoDB  
- **Database:** MongoDB (persistent volume enabled)  
- **Deployment:** EKS cluster with Load Balancer for external access  

🚀 Project Journey  

From scratch to deployment, the project covers:  
- IAM setup for secure access  
- EKS cluster creation  
- Load Balancer integration  
- Docker image build and push to ECR  
- CI/CD automation and monitoring integration  


🧭 Step-by-Step Implementation  

🪪 Step 1: IAM Configuration  

Create an IAM user for EKS management.  

-  aws iam create-user --user-name eks-admin
-  aws iam attach-user-policy --user-name eks-admin --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

 Generate Access Key and Secret Access Key, then configure them:

- aws configure


💻 Step 2: EC2 Instance Setup

 - Launch an Ubuntu EC2 instance in your preferred region (e.g., us-west-2).
 - Connect via SSH: ssh -i "your-key.pem" ubuntu@<public-ip>

⚙️ Step 3: Install AWS CLI v2

- curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
- sudo apt install unzip -y
- unzip awscliv2.zip
- sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
- aws --version

🐳 Step 4: Install Docker & Docker Compose  ( this is by your choice if need than we can install) 

- sudo apt-get update -y
- sudo apt install docker.io -y
- sudo chown $USER /var/run/docker.sock
- docker --version
- docker ps

☸️ Step 5: Install kubectl

- curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
- chmod +x ./kubectl
- sudo mv ./kubectl /usr/local/bin
- kubectl version --short --client

⚡ Step 6: Install eksctl

- curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
- sudo mv /tmp/eksctl /usr/local/bin
- eksctl version

☁️ Step 7: Create EKS Cluster  ( here we need to take care that need to choose the region and cluster name that we req and the ec2 - type and size )
eksctl create cluster \
  --name three-tier-cluster \
  --region ap-south-1 \
  --node-type t2.medium \
  --nodes-min 2 \
  --nodes-max 2

- aws eks update-kubeconfig --region ap-south-1 --name three-tier-cluster
- kubectl get nodes

📦 Step 8: Apply Kubernetes Manifests

- kubectl create namespace three-tier
- kubectl apply -f Kubernetes-Manifests-Files/

⚙️ Step 9: Install AWS Load Balancer Controller IAM Policy

- curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json

- eksctl utils associate-iam-oidc-provider --region us-west-2 --cluster three-tier-cluster --approve

eksctl create iamserviceaccount \
  --cluster three-tier-cluster \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn arn:aws:iam::<your-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve \
  --region ap-south-1


⚙️ Step 9: Install AWS Load Balancer Controller IAM Policy

- curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json

- eksctl utils associate-iam-oidc-provider --region us-west-2 --cluster three-tier-cluster --approve

eksctl create iamserviceaccount \
  --cluster three-tier-cluster \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn arn:aws:iam::<your-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve \
  --region us-west-2

## NOTES 

- need to make sure all the data that is needed given properly in the manifest file the ports and all 
