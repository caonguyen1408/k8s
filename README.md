# K8s Tip
### Basic(pod,deployment,service)
##### [Kubectl Usage Conventions](https://kubernetes.io/docs/reference/kubectl/conventions/ "Heading link")
### Super basic kube-system
- [ ] coredns
- [ ] etcd-controlplane
- [ ] kube-apiserver-controlplane
- [ ] kube-controller-manager-controlplane
- [ ] kube-proxy
- [ ] kube-scheduler-controlplane
- [ ] weave-net (CNI)

#### POD
-------------
##### Create an NGINX Pod
` kubectl run nginx --image=nginx`
##### Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
` kubectl run nginx --image=nginx --dry-run=client -o yaml`
#### Deployment
-------------
##### Create delopyment
` kubectl create deployment --image=nginx nginx`

##### Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`

##### Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml`

###### Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment.

` kubectl create -f nginx-deployment.yaml` 

###### In k8s version 1.19+, we can specify the --replicas option to create a deployment with 4 replicas.

`kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml`

#### Service
-------------
##### Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379

`kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml`
(This will automatically use the pod's labels as selectors)
 Or
`kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml`

(This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

##### Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:
`kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml`

(This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

`kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml`

(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the kubectl expose command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

`kubectl expose deployment ingress-controller --type=NodePort --port=80 --name=ingress --dry-run=client -o yaml > ingress.yaml`
