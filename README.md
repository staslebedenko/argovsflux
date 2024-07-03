# argovsflux

Pre-Requisites
You will need the following command line tools installed to proceed with getting started:
- Kubernetes binary https://kubernetes.io/docs/tasks/tools/
- Argocd binary https://argo-cd.readthedocs.io/en/stable/cli_installation/ or choco install argocd-cli
- Azure CLI (optional, but recommended) https://docs.microsoft.com/cli/azure/install-azure-cli?WT.mc_id=opensource-52942-jessde
- Access to your AKS cluster https://docs.microsoft.com/azure/aks/kubernetes-walkthrough?WT.mc_id=opensource-52942-jessde#connect-to-the-cluster

let's login to our container registry 
```cmd
az login

az account show
az acr login --name contlandregistry
```

```cmd
az login

az account show
az acr login --name fluvsargo
az aks get-credentials --resource-group demo-conference --name fluxvsargo
kubectl config use-context fluxvsargo
kubectl get all --all-namespaces
```

```cmd
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl get all -n argocd
kubectl patch svc argocd-server -n argocd -p "{\"spec\": {\"type\": \"LoadBalancer\"}}"  
kubectl get services --namespace argocd argocd-server --output jsonpath='{.status.loadBalancer.ingress[0].ip}
kubectl port-forward svc/argocd-server -n argocd 8080:443

```
Port forwarding above is used to connect to argo from your local machine

PS command below to get a password for admin of Argo
```PS
 [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String((kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}")))
```

in separate cmd start port forwarding
```
kubectl port-forward svc/argocd-server -n argocd 8080:443  
```

then create a namespace and login to argo with admin login and password from PS commande above
```
kubectl create namespace bookapp

argocd login localhost:8080 --username admin --password youwillneverguess 

argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace bookapp  

argocd app get guestbook

argocd app sync guestbook
```
