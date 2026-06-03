# terraform EKS
1. Folder **eks** consists of terraform files to provision in eks in aws
cmd :
	terraform validate
  terraform plan
  terraform apply --auto-approve


### After eks provissioning update context ###
aws eks update-kubeconfig --name <cluster-name> --region <region-name> 

# nginx
1. folder consists all the yaml to for the **nginx**
   nginx-namespace
   nginx-deployment
   nginx-service

# install Helm
1. Download Helm install script
			curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
   
2. Give execute permission
	 chmod 700 get_helm.sh

3. Install Helm
		./get_helm.sh

4. Verify installation
		helm version

# Install Ingress Controller
1. add repo in helm
   	helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

2. cmd to update helm repo
   	helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx \
--namespace ingress-nginx \
--create-namespace

4. Apply the ingress yaml  file after ingress controller installation 
		kubectl apply -f nginx-ingress

5. to chech ingress installation
		kubectl get pods -n ingress-nginx
		kubectl get svc -n ingress-nginx
		kubectl get ingress -n ns-nginx

# Install Argocd
1. Create Namespace for argocd
   		kubectl create namespace argocd

2. Install ArgoCD:
	kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

3. Kubernetes creates:

ArgoCD server
Repo server
Redis
Controller
UI

4. Check pods:
     kubectl get pods -n argocd

5. Expose ArgoCD UI
   	kubectl get svc -n argocd

6. argocd-server ClusterIP Change it to LoadBalancer:
   	kubectl patch svc argocd-server \
-n argocd \
-p '{"spec": {"type": "LoadBalancer"}}'

7. Check arggocd service:
   	kubectl get svc -n argocd

8. username : admin and to Get Default Password
   kubectl -n argocd \
get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d


	
	 
