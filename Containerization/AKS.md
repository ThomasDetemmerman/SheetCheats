# Login
```
brew install kubectl # brew upgrade kubectl # installs version 1.19.4
az login
az account set --subscription <subscription>
az aks get-credentials --name <aks name> --resource-group <rg name> --admin
kubectl get nodes
kubectl get pods -n myNamespace

```

# RBAC
az role assignment list --assignee <id> --scope /subscriptions/<subid>/resourcegroups/<rg>/providers/Microsoft.ContainerService/managedClusters/<clustername/namespaces/<mynamespace>
