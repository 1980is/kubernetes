# Kubernetes and Cloud Native Associate

## General Info

Get bash completion for kubectl: ``source <(kubectl completion bash)`` \
Get help: ``kubectl explain pod``and ``kubectl explain pod.spec`` and ``kubectl explain --recursive pod.spec``This is a tree shape hierachy of all the resources that are available.

## Stuff to install Minikube, Cubectl

https://minikube.sigs.k8s.io/docs/start/ \
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

## Deployment
``kubectl create deploy myweb --image=nginx`` \
``kubectl get deploy myweb -o yaml``

## Running Pods
Generally you shouldn't run naked Pods. \
Run a Pod with the name armann. ``kubectl run armann --image=nginx`` \
Generate a YAML file which will allow you to run the application in a declarative way. ``kubectl run armann --image=nginx --dry-run=client -o yaml > armann.yaml`` \
Run the application: ``kubectl create -f armann.yaml``

## Kubectl Commands

``kubectl get all`` \
See namespaces:``kubectrl get ns`` \
See labels for resources: ``kubectl get all --show-labels`` \
``kubectl get endpoints``
Use a specific label go the specific information about an app: ``kubectl get all --selector app=nginxopen`` \
Show available resources: ``kubectl api-resources`` \
``kubectl describe svc nginxopen``
Show versions: ``kubectl api-versions`` \
Get more information about resource properties: ``kubectl explain <resource>`` \
Show fields in the resource spec: ``kubectl explain <resource>.spec`` \
Show all field available: ``kubectl explain --recursive <resource>``

## Application Resources

### Deployment
The standard resource for running scalable applications with the option to perform zero-downtime application updates. \
``kubectl create deployment nginxfarm --image=nginx --replicas=3 --dry-run=client -o yaml > nginxfarm.yaml``

### Stateful
An alternative to the Deployment Resources. Commonly used for stateful applications like databases.

### DaemonSet
This ensures that one application instance is started on each cluster node.

### Job
Used for singe-shot applications.

## Scalability

### Manually Adjusting Scalability

To manually scale the number of Pods in a Deployment or ReplicaSet use ``kubectl scale``\
You can also use ``kubectl edit`` to change the number of Pods in a ReplicaSet or Deployment. \
To scale the number of nodes in a cluster, add the new node using ``kubeadm join`` command. \

### Autoscaling

Install the metrics server: ``kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml``\
\
``kubectl edit deploy -n kube-system metrics-server`` \
You need to add this line to the "containers" - args:
``--kubelet-insecure-tls`` \
To see metrics for pods and verify the metric server is working. ``kubectl top pods``

## Networking

### Networking Service Types

**Cluster IPs** are only accessible on the cluster node. If you are using Minikube, you can ssh into Minikube and connect to the Cluster IP. ``minikube ssh`` \
**NodePort** port forwads incoming traffic from node ports to a CluterIP IP address to provice external access. \
**LoadBalancer** works in an environment that has an API to configure a loadbalancer instance.
\
**Headless** is a service without an IP address. For internal use between frontend and backend applications.

### Giving access to ports

Ingress provides external http/https routes to services within the cluster. \
You have to setup an ingress controller. \
\
Let's use the Minikube Ingress addon. \
``minikube addons list`` Select the ingress. \
``kubectl get all -n ingress-nginx`` \
\
**NetworkPolicy** can filter incoming and outgoing traffic. Traffic between Namespaces is not filtered by default. NetworkPolicy can be used to apply inter-namespace traffic filtering.

**Opening a port on a Pod.**

Let's first setup a Pod to play around with. \
``kubectl create deployment nginxopen --image=nginx`` \
Let's expose a service/port for that app. \
``kubectl expose deployment nginxopen --port=80``
