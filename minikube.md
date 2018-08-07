# Minikube/Kubernetes Tutorials
## Creating a Cluster
````bash
# check minikube is properly installed
minikube version

# start the cluster
minikube start

# Interact with kubernetes , we need tu use kubectl
kubectl version

# check cluster details
kubectl cluster-info

# View nodes in the cluster
kubectl get nodes
````

## Using kubectl to create a deployment
Once the application instances are created, a Kubernetes Deployment Controller continuously monitors thoese instances. If the Node
hosting an instance goes down or is deleted, the deployment controller replaces it. This provides a self-healing mechanism to
address machine failure or maintenance.

````bash
# use --help after the command to get additional info about possible parameters
kubectl version
kubectl get nodes

# kubectl run --help
# run creats a new deployment, we need to provide deployment name and app image location, we also want to run the app on a specific port
# what the the command does below is
# 1. search for a suitable node where an instance of the application could be run
# 2. scheduled the application to run on that node
# 3. configured the cluster to reschedule the instance on a new node when needed
kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080

# get deployments
kubectl get deployments

# pods: running inside kubernetes, on a private, isolated network.
# By default they are visible from other pods and services within the same kubernetes cluster, but not outside that network.
# The kubectl command can create a proxy that will forward communications into the cluster-wide, private network.(open in another terminal)
kubectl proxy
curl http://localhost:8001/version

# The API Server will automatically create an endpoint for each pod, based on the pod name, that is also accessible by the proxy.
export POD_NAME=$(kubectl get pods -o go-template --template '{{range.items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy
````

## Explore Your App
