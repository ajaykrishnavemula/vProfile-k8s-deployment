# vProfile Project: Kubernetes Deployment on AWS
(kOps)
Deploy the **vProfile web application** (Nginx, Tomcat, RabbitMQ, Memcached, MySQL) on a
scalable, production-grade **Kubernetes cluster** managed via **kOps and AWS EC2**, using
**NGINX Ingress** for external access.
---
## ■■ Architecture Summary
- All services (Tomcat, MySQL, RabbitMQ, Memcached) run in **separate pods and deployments** for
isolation and scalability.
- Internal traffic is routed via **ClusterIP services**.
- **MySQL** uses a **PersistentVolumeClaim** with a **StorageClass** backed by **AWS EBS** for
persistent data.
- **Secrets** hold credentials for secure injection.
- **NGINX Ingress Controller (with AWS ALB)** exposes your app externally via DNS.
---
## ■ Diagrams
- Kubernetes Logical Architecture
- Network & Traffic Flow
---
## ■ Step-by-Step Workflow
### 1■■ Launch AWS EC2 Instance (Admin/Jump
Box)Start a clean **Amazon Linux/Ubuntu EC2**.
SSH into it.
---
### 2■■ Install Required Tools
Set up:
- **AWS CLI** (`aws configure`)
- IAM permissions for EC2, S3, VPC, Route53
- **kubectl**
- **kOps**
---
### 3■■ Use kOps to Create Your Kubernetes
Cluster
**Preparation:**
- Create **S3 bucket** for kOps state
- Create **SSH keypair**
- Create **Route53 hosted zone**
**Run Commands:**
- Provision **1 master** and **2 worker nodes**
Validate cluster status with:
```
kops validate cluster
```---
### 4■■ Set Up DNS for Cluster Access
- Retrieve API/public endpoint from Route53 or AWS Console.
- Point your domain (e.g., `vprofile.hhkinfoteck.xyz`) to the AWS ALB endpoint via **GoDaddy DNS
records**.
---
### 5■■ Install NGINX Ingress Controller
Create namespace and deploy the AWS-optimized NGINX ingress controller:
```
kubectl create namespace ingress-nginx
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provide
r/aws/deploy.yaml
```
Confirm pods/services in `ingress-nginx` namespace are running:
```
kubectl get pods -n ingress-nginx
```
---
### 6■■ Clone vProfile Source and Prepare
Manifests
```
git clone
cd manifests/
```Check the YAML files required (screenshot reference included).
---
### 7■■ Apply Kubernetes Manifests (Deploy App)
Deploy all components:
```
kubectl apply -f .
```
Verify:
```
kubectl get pods,svc,pvc,secrets
```
---
### 8■■ Access the App via DNS and Ingress
- NGINX Ingress Controller provisions an **ALB**.
- Ingress maps to **Tomcat service**.
Retrieve ALB hostname:
```
kubectl get ingress
```
Point **GoDaddy DNS** to ALB hostname.
Access your app at:
```http://vprofile.hhkinfoteck.xyz
```
---
## ■ Kubernetes Manifests Structure
| Component | Purpose |
|------------|----------|
| **Secrets** | DB and RabbitMQ credentials |
| **PVC + StorageClass** | MySQL persistence (EBS) |
| **Deployments/Pods** | Tomcat, MySQL, RabbitMQ, Memcached |
| **ClusterIP Services** | Internal communication |
| **Ingress YAML** | External routing (NGINX + ALB) |
---
## ■ Troubleshooting & Validation
```
kubectl get nodes
kubectl get all
kubectl get pvc
kubectl describe ingress
```
---
## ■ Diagrams & Screenshots Reference
- Kubernetes Cluster Architecture → `Kubernetes-Cluster.jpg`- kOps Setup → `Pasted-Graphic-1.jpg`
- Ingress YAML → `appingress.yaml.jpg`
- App Access & Network Flow → `Pasted-Graphic-3.jpg`
- Manifest Directory Screenshot → (Terminal reference)
---
## ■ Summary
**vProfile Kubernetes deployment in AWS using kOps and NGINX Ingress Controller** ensures a
**scalable, robust, and secure orchestration** for modern containerized workloads.
This guide follows best practices for:
- Multi-tier app orchestration
- Persistent storage
- Secret management
- External accessibility