# Kubernetes and Cloud Native Associate

## General Info

https://kubernetes.io/docs/reference/kubectl/cheatsheet/

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
``kubectl get pods -o wide``
``kubectl describe pod/oreilly`` \
``kubectl get endpoints`` \
``kubectl describe svc nginxopen``
Show all field available: ``kubectl explain --recursive <resource>``

**Namespaces:** \
``kubectrl get ns`` \
``kubectl -- create namespace kcna``

**Labels:** \
See labels for resources: ``kubectl get all --show-labels`` \
See labels for pods: ``kubectl get pods --show-labels``
Use a specific label go the specific information about an app: ``kubectl get all --selector app=nginxopen`` \
Label a pod named oreilly: ``kubectl label pods oreilly production=yes`` \
``kubectl get pods -l production=yes``

**API:** \
Show versions: ``kubectl api-versions``

**Resources:** \
Get more information about resource properties: ``kubectl explain <resource>`` \
Show fields in the resource spec: ``kubectl explain <resource>.spec`` \
Show available resources: ``kubectl api-resources`` \

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

Install the metrics server: ``kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml``
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
``kubectl expose deployment nginxopen --port=80`` \
``kubectl get services`` If it's a "CLUSTER-IP" you can't directly connect to port 80. You have to come from a cluster ip.\
\
Let's change that by doing ``kubectl edit services nginxopen``. Change type: ClusterIP to NodePort. Now we must access it using the Node IP address that's running the service.

## Storage

## Service Mesh

A service mesh is a dedicated infrastructure layer for making communication between services safe, observable, and reliable.
A service mesh is a special layer for handling service-to-service communication. The service here is typically a microservice running in a container orchestrated by Kubernetes. The proxies in a service mesh form the data plane. This is where networking rules are implemented and shape the traffic flow.
These rules are managed centrally in the control plane of the service mesh. This is where you define how traffic flows from service A to service B and what configuration should be applied to the proxies.

So instead of writing code and installing libraries, you just write a config file where you tell the service mesh that service A and service B should always communicate encrypted. The config is then uploaded to the control plane and distributed to the data plane to enforce the new rule.
