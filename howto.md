# Start Minikube cluster with ingress
``` bash
minikube delete
minikube start
minikube addons enable ingress
```

# Install argocd and expose service
``` bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
watch kubectl get all -n argocd
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
minikube service argocd-server -n argocd
```

# Initialize argocd client
``` bash
argoURL=$(minikube service argocd-server -n argocd --url | tail -n 1 | sed -e 's|http://||')
argoPass=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
echo $argoURL admin:$argoPass
argocd login --insecure --grpc-web $argoURL  --username admin --password $argoPass
argocd account update-password
```
