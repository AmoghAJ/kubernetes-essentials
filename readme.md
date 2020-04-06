# Kubernetes Basics

## important components
On Master
* **API Server** : Main communication hub, rest API service.
* **Scheduler**: Responsible for assigning worker nodes. 
* **Controller** : Backend for API server.
* **etcd**: data store that stores the cluster configuration.

On Nodes
* **kublet**: Runs and manages pods and talks to API server.
* **kube-proxy**: load balancing traffic between components.
* **container runtime**: program which runs containers/ responsible for communicating to container provider. (eg. docker etc)

Installing Kubernets: [Click](docs/install.md)

Simple nginx pod:
```
cat << EOF | kubectl create -f -
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
EOF
```

View pod:
> This pod will be created in default namespace hence,
```
kubectl get pods
```

### API Primetives: 
#### Required fields:
* **apiVersion** - Which version of the Kubernetes API you’re using to create this object.
* **kind** - What kind of object you want to create.
  
* **metadata** - Data that helps uniquely identify the object, including a name string, UID, and optional namespace.
* **spec** - What state you desire for the object.    
##### Compponent status
```
kubectl get componentstatus
```

Create deployment from yaml. [nginx-deployment.yaml](sample/nginx-deployment.yaml)
```
kubectl create -f nginx-deployment.yaml
```
get full yaml back
```
kubectl get deployments nginx-deployment -o yaml
```
Show all pod labels.
```
kubectl get pods --show-labels
```
adding label to the pod
```
kubectl label pod <pod-name> env=prod
```
find pod with label
```
kubectl get pods -L env=prod
```
view specific label of all pods
```
kubectl get pods -L env
```
Use field selector
```
kubectl get pods --field-selector status.phase=Running
```

### Some important command reference
view Kubernetes cluster information
```
ubectl cluster-info
```
view Kubernetes kubeconfig settings
```
kubectl config view
```
Show all node details
```
kubectl describe nodes
```
Show all pods details
```
kubectl describe pods 
```
show all service details in all namespaces
```
kubectl get services -all-namespaces
```
View all resources
```
kubectl api-resources -o wide
```

### Validating kubernetes cluster
Run simple nginx deployment
```
kubectl run nginx --image=nginx
```
view deployments
```
kubectl get deployments
```
view pors
```
kubectl get pods
```
Port forward test
```
kubectl port-forward <pod name> 8081:80
```
> From same host can check result on port 8081 `curl localhost:8081`

View logs
```
kubectl logs <pod name>
```
Running command inside the pod
```
kubectl exec -it <pod name> -- nginx -v
```
Create service using deployment
```
kubectl expose deployment nginx --port 80 --type NodePort
```
View all services
```
kubectl get svc
```
Get detaied information about nodes
```
kubectl describe nodes
```
Get detaied information about pods
```
kubectl describe pods
```