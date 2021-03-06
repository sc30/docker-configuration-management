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

# get logs
kubectl logs [pod_name] -c [container_name_in_pod]

# execute command on the container
kubectl exec [pod_name] env
# Only one container in the pod
kubectl exec -it [pod_name] bash
# Access one of the containers in the pod
kubectl exec -it [pod_name] -c [container_name_in_pod] bash
````

## Explore Your App
### Pods
Pod is a Kubernetes abstraction that represents a group of one or more application containers(Docker or rkt), and some shared resources for those containers, e.g:
* Shared storage, as Volumes
* Networking, as a unique cluster IP address
* Information about how to run each container, such as the container image version or specific ports to use
Some notes:
* A pod contains different application containers which are relatively tightly coupled.
* Pods are the atomic unit on kubernetes platform, when we create a deployment on kubernetes, that deployment creates Pods with containers inside them(as opposed to creating containers directly.)
### Nodes
A Pod always runs on a Node where a Node is a worker machine[Node can be either a virtual or a physical machine, depending on the cluster]. Each Node is managed by the master, a node can have multiple pods, and the Kubernetes master automatically handles scheduling the pods across the Nodes in the cluster.
Every Kubernetes Node runs at least:
* Kubelet: a process responsible for communication between the kubernetes master and the node, it manages the pods and the containers running on a machine
* A container runtime(Docker, rkt) responsible for pulling the container image from a registry, unpacking the container, and running the application.
### Troubleshooting
* kubectl get - list resources
* kubectl describe - show detailed information about a resource
* kubectl logs - print the logs from a container in a pod
* kubectl exec - execute a command on a container in a pod

## Expose Your App Publicly
A service in Kubernetes is an abstraction which defines a logical set of Pods and a policy by which to access them. Services enable a loose coupling between dependent Pods. The set of Pods target by a service is usually determined by a LabelSelector.

Each Pod has a unique IP address, but they are not exposed outside the cluster without a service. Service can be exposed in different ways by spcifying a type in thet ServiceSpec
* ClusterIP(default): make the service only reachable from within the cluster
* NodePort
* LoadBalancer
* ExternalName

````bash
# Step 1: Create a new service
kubectl get pods
kubectl get services

kubectl expose deployment kubernetes-bootcamp --type=NodePort --port=8080
kubectl describe services kubernetes-bootcamp
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT

curl $(minikube ip):$NODE_PORT

# Step 2: Using labels
# The deployment automatically created a label for our pod, to view
kubectl describe deployment
# Labels: run=kubernetes-bootcamp
# -l, --selector=
kubectl get pods -l run=kubernetes-bootcamp
kubectl get services -l run=kubernetes-bootcamp

# Get Pod name
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
# Apply a new label
kubectl label pod $POD_NAME app=v1
# A new label will be pinned to our Pod
kubectl describe pods $POD_NAME
kubectl get pod -l app=v1

# Step 3: Deleting a service, Pod is still running
kubectl delete service -l run=kubernetes-bootcamp
# use our defined label seems not be able to delete service, below won't work
kubectl delete service -l app=v1
# Confirm that the service is gone:
kubectl get services
# Confirm that route is not exposed anymore
curl $(minikube ip):$NODE_PORT
# This proves that app is not reachable anymore from outside of the cluster. We can confirm that the app is still running with a curl inside the pod
kubectl exec -ti $POD_NAME curl localhost:8080
````

## Scale Your App
