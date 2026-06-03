# Terraform EKS with NGINX, Ingress Controller, and ArgoCD

This project contains Terraform configuration to provision an EKS cluster in AWS and deploy NGINX using Kubernetes manifests, Ingress Controller, Helm, and ArgoCD.

---

# 1. Provision EKS using Terraform

The **`eks`** folder contains Terraform files to provision an EKS cluster in AWS.

### Terraform Commands

```bash
terraform validate
terraform plan
terraform apply --auto-approve
```

### Update Kubernetes Context After EKS Provisioning

After the EKS cluster is created, update your `kubectl` context:

```bash
aws eks update-kubeconfig --name <cluster-name> --region <region-name>
```

Example:

```bash
aws eks update-kubeconfig --name my-eks-cluster --region ap-south-1
```

---

# 2. Deploy NGINX Application

The **`nginx`** folder contains all Kubernetes YAML files required for NGINX deployment.

### Files Included

* `nginx-namespace.yaml`
* `nginx-deployment.yaml`
* `nginx-service.yaml`

### Apply NGINX YAML Files

```bash
kubectl apply -f nginx-namespace.yaml
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml
```

### Verify Deployment

```bash
kubectl get pods -n ns-nginx
kubectl get svc -n ns-nginx
```

---

# 3. Install Helm

### Step 1: Download Helm Installation Script

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```

### Step 2: Give Execute Permission

```bash
chmod 700 get_helm.sh
```

### Step 3: Install Helm

```bash
./get_helm.sh
```

### Step 4: Verify Installation

```bash
helm version
```

---

# 4. Install NGINX Ingress Controller

### Step 1: Add Helm Repository

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### Step 2: Update Helm Repository

```bash
helm repo update
```

### Step 3: Install Ingress Controller

```bash
helm install ingress-nginx ingress-nginx/ingress-nginx \
--namespace ingress-nginx \
--create-namespace
```

### Step 4: Apply Ingress YAML File

Apply the ingress YAML **after** the Ingress Controller installation.

```bash
kubectl apply -f nginx-ingress.yaml
```

### Step 5: Verify Ingress Installation

```bash
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
kubectl get ingress -n ns-nginx
```

---

# 5. Install ArgoCD

### Step 1: Create Namespace for ArgoCD

```bash
kubectl create namespace argocd
```

### Step 2: Install ArgoCD

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Step 3: Components Created by Kubernetes

After installation, Kubernetes creates the following components:

* ArgoCD Server
* Repo Server
* Redis
* Application Controller
* UI

### Step 4: Verify Pods

```bash
kubectl get pods -n argocd
```

### Step 5: Check ArgoCD Service

```bash
kubectl get svc -n argocd
```

### Step 6: Change `argocd-server` Service Type to LoadBalancer

```bash
kubectl patch svc argocd-server \
-n argocd \
-p '{"spec": {"type": "LoadBalancer"}}'
```

### Step 7: Verify ArgoCD Service

```bash
kubectl get svc -n argocd
```

### Step 8: Login Credentials

**Username:**

```text
admin
```

**Get Default Password:**

```bash
kubectl -n argocd \
get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d
```

---

# Useful Commands

### Check Kubernetes Nodes

```bash
kubectl get nodes
```

### Check All Resources

```bash
kubectl get all -A
```

### Check Ingress

```bash
kubectl get ingress -A
```

### Check Services

```bash
kubectl get svc -A
```
