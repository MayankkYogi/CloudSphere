# What is Kubernetes
1. It is an open source container management tool which automates container deployment, container scaling and load
balancing. It schedules, runs and manages containers which are running on virtual/physical/cloud machines.
2.  It support and well worked with microservice architecture rather then monolithic.
3. We need kubernetes where we have 100s of containers, managing large no of containers is easy for k8s.
4. It does not create images but it is managing the containers and create cluster of it.
5. Developed by Google in 2014 written in GO language but later donated to CNCF (cloud native computing foundation).
6. We can use JSON and YAML to write scripts.

# Cloud based k8s providers
1. GKE: Google kubernetes service
2. AKS: Azure kubernetes service
3. Amazon EKS: Elastic kubernetes service

# Why kubernetes comes into picture
1. Containers cannot communicate with each other
2. Auto scaling and load balancing was not possible

# Advantages
1. It is more scalable, we can delete or add containers according to business requirement.
2. Enables auto balancing. Add or remove containers accordingly.
3. Platform independant: cloud/VM/physical
4. Support public, private and hybrid cloud
5. Horizontal and Vertical scaling
6. Fault Tolerance
7. Rollback
8. Health Monitoring of containers
9. Community Support and Knowledge Sharing

# K8S Vs Docker Swarm

### Installtion
K8S: Complicated and series of manual steps\
Swarm: Fast and easy

### Supports
K8S: It can work with any type of container docker, rocket\
Swarm: Support only docker container

### GUI
K8S: No GUI\
Swarm: GUI available

### Volume
K8S: Only shared with container inside same POD\
Swarm: Can be shared with any containers

### Auto Scaling
K8S: Support Vertical and horizontal\
Swarm: Not Support


# Architecture 

### Master Node
1. Responsible for managing k8s cluster
2. It is an entrypoint for all admin tasks
3. There can be one or more master node but one will be the primary leader

### Components in master node
### kube-api-server:
1. This is essentially the entry-point to the Kubernates cluster
2. It autoscale as per request load it recives.
3. api-server is frontend of control plane

### etcd:
1. It stores metadata and status of cluster
2. It is consistent and high available store
3. Fully Replicated: The entire state is available on every node in the cluster

### kube-scheduler:
1. When user make request for pod creation and management of pods, scheduler will going to take action on it
2. Handles pod creation and manage it
3. It automatic creates pods according to requirement
4. It also helps tp find the best node where we can put or assign new pod if not written manually by user

### Controller Manager
1. Make sure actual state is equal to desired state
2. If k8s in cloud then it is cloud contoller manager and when not then it is kube controller manager

### Components on master that run controller
As we have controller based on specific tasks:
1. Node-Controller: For checking the cloud providers to determine if a node has been detected in the cloud or not
2. Route-Controller: Responsible for setting up network routes on your cloud
3. Service-Controller: Responsible for load balancers  on cloud
4. Volume-Controller: For creating, attaching and mounting volumes.

### Worker Node
### Kubelet
1. It is agent running on node (agent in azure)
2. Listen to master(eg: pod creation request) specifically through kube-controller
3. It uses port:10255
4. Send success/fail report to master

### Container Engine
1. It works with kubelet
2. Start/Stop containers

### Kube-Proxy
1. It assign IP to each Pod
2. It run on each node and this make sure that each pod get its unique IP


### POD
1. Smallest unit in k8s
2. It is a group of one or more containers that are deployed together on same host
3. A cluster is group of pods
4. A cluster has atleast one master and one worker node
5. It consists of one or more tightly coupled container means if one container fails then other will too.
6. Pod run on node which is control by master
7. Usually one pod contain one container

### Fundaments of PODS
1. When a pods get created, it is schedules to run the node in your cluster
2. The pod remains on the node untill the process is terminated, node crash, object is deleted
3. If node deleted or crash so pod on that will also deleted after certain default time period
4. For new node created existing POD will not reschedules instead it will create a new POD with different UID.

### If Multi-Container POD
1. Connect each other via local host not with unique IP address, as POD have IP not container
2. Share same volume
3. In this either all container will work or nothing
4. Container from 2 different node will not combine to create a single POD

### POD limitations
1. No auto scaling
2. If POD crashes then what?

### We need to add plugins or higher level objects to support these limitations
1. Replication Set: Helps in scaling
2. Deployment: Versioning and Rollbacks
3. Service: Helps to give static or constant IP
3. Volume: Provide external storage. If POD crash then volume will not destroyed

### NOTE:
- kubectl command used in single cloud (AWS, Azure)
- kubeadm command used in on-prem
- kubefed command used in hybrid
- Requirement for creating a master node: 2 vCPU and 4 GB RAM

# Setup master and worker node on AWS
- Launch 3 instances (one master node and two worker node)
- Edit Security group and allow all traffic
- Run below commands in all 3 instances/VMs
- Choose Ubuntu free tier instance which is t2.medium
### Commands
```
sudo su
apt-get update
apt-get install apt-transport-https  -> to secure communication between nodes  in same cluster
apt install docker.io -y
docker --version
systemctl start docker  -> systemctl uses in updated ubuntu version
systemctl enable docker

Setup open GPG key. This is required for intra cluster communication. It will be added as source key on the node. 
It is simple a key to verify the authenticity of upcoming request. When k8s send information to our host it is going to accept the information because this key is present in the source key

sudo curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add 
nano /etc/apt/sources.list.d/kubernetes.list -> nano is used to create and edit a file
deb http://apt.kubernetes.io/ kubernetes-xenial main
ctrl+x
enter Y
press enter
apt-get update  -> now install the packages from kubernetes.list
apt-get install -y kubelet kubeadm kubectl kubernetes-cni  -> install multiple packages 

Configure THE MASTER NODE (IN MASTER) (Run on master instance)
kubeadm init  -> intialize master node (copy the kubeadm... output command)
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/v0.22.2/Documentation/k8s-old-manifests/kube-flannel-rbac.yml

CONFIGURE WORKER NODES (IN NODES)
(copy the kubeadm... output command)

kubectl get nodes -> run in master to check whether node is connected or not

```

# Objects

1. K8S uses objects to represent the state of the cluster.
2. Objects contains what a containerized application are running on which node.
3. Also for information about the policies like restart policies, fault tolerance etc treat as object
4. After creating objects k8s make sure that system will constantly work to esure that object exist and maintain desired cluster state
5. There are 2 field of object one is object specification and another is object status.
6. Object spec is what we describe in the manifest file eg: we want tomcat server, port 80, 2 replica sets etc.
7. Object status is actual state that is provided by kubernetes after running the manifest file.
8. It represent as JSON or YAML files(manifest in k8s).

### Some object in k8s:
- POD
- Service
- Volume
- Secrets
- Job
- Deployment
- Replica Sets
- Namespace

# K8S objects management
The kubectl command line tool supports serveral different ways  to create and manage kubernetes objects

1. Imperative Commands: line by line commands that we run on cmd.
2. Declarative object configuration: individual files(yml/json) mostly used in PROD enviroment.

# Minikube
It is a lightweight k8s implementation that creates a VM on your local machine and deploy a simple cluster containing only one node. Here both master and worker node can run on same machine no need to create separate VM for master and worker node. Used for learning, development and testing.

### kubectl
It is a k8s command line tool. It allows to run command against your clusters. We can use this to deploy, inspect and manager cluster and also view logs.

```
sudo su
Now install docker
sudo apt update && apt -y install docker.io
# install Kubectl
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x ./kubectl && sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version
# Now install Minikube
curl -Lo minikube https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbkVLdUM4OGlxX2Z5b0Q3bmwta3lZbVd2RlVJUXxBQ3Jtc0trekE2M2JxR2s4TGhUS1prdDVrdUlwWHhocklUS05idXRydTVNMnJld1FhUzVrZHl3eVB1VVI4UWRVUFB6SmJEYXlGRmJlcERGZjVqOEZzWHdDRU1sazBXd3RUWjNPMkpFVzlERjU3Y3FLa29BM3R0Zw&q=https%3A%2F%2Fstorage.googleapis.com%2Fminikube%2Freleases%2Flatest%2Fminikube-linux-amd64&v=hV8zi3vdQqk && chmod +x minikube && sudo mv minikube /usr/local/bin/

apt install conntrack ---> need to run to support minikube
minikube start --vm-driver=none
minikube status
kubectl version
kubectl get nodes

```
```
# create pods

NOTE: rm -rf <yml file name> ---> to delete file from ubuntu

vi pod1.yml
kind: Pod
apiVersion: v1
metadata:
  name: testpod1
spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo cloudsphere; sleep 5 ; done"]
    - name: c01
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Hello-Mayank; sleep 5 ; done"]
  restartPolicy: Never   #default to always

:wq  ---> to save and exit

kubectl apply -f pod1.yml  ---> to execute yml file
kubectl get pods  ---> to view pods
kubectl get pods -o ---> to check in which node this pod is created
kubectl describe pod testpod1  --> to view more details
OR
kubectl describe pod/testpod1  --> to view more details
kubectl delete pod testpod1  ---> to delete pod
kubectl delete -f pod1.yml  ---> to delete pod via yml
kubectl logs -f testpod1 -c c00  ----> to check the logs of container
kubectl exec testpod1  -c c00 -- hostname -i ---> to check the IP of pod
kubectl exec testpod1 -it -c c00 -- /bin/bash   ----> to work in container
ps -ef
exit
```

```
# use comments or annotation for the description

vi pod2.yml
kind: Pod
apiVersion: v1
metadata:
  name: testpod2
  annotations:
    description: First POD is created 
spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo cloudsphere; sleep 5 ; done"]
  restartPolicy: Never   #default to always

:wq  ---> to save and exit

kubectl apply -f pod2.yml  ---> to execute yml file
kubectl describe pod testpod2
```

```
# POD ENVIRONMENT  VARIABLES


kind: Pod
apiVersion: v1
metadata:
  name: environments
spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Hello-Mayank; sleep 5 ; done"]
      env:                        # List of environment variables to be used inside the pod
      - name: MYNAME
        value: Mayank

kubectl apply -f pod3.yml  ---> to execute yml file
kubectl describe pod environments
kubectl exec environments -it -c c00 -- /bin/bash   ----> to work in container
env
echo $MYNAME

```

```
# POD WITH PORTS

kind: Pod
apiVersion: v1
metadata:
  name: podPorts
spec:
  containers:
    - name: c00
      image: httpd
      ports:
       - containerPort: 80  

kubectl apply -f pod4.yml  ---> to execute yml file
kubectl describe pod podPorts
kubectl get pods -o wide --> we can get podIP
curl podIP

```
# Labels
Labels are mechanism which we can use to organise the kubernetes objects. This labels we can apply on node, pod or any objects. It is similar to tag which we used in Azure.

### Why Labels
Suppose there are 100 containers of different project and we only want a specific project cluster then we can use lables to get the desired results.

Labels are key-value pair without any predefined meaning. Multiple labels can be added to single objects. We can also delete pod using pod name, yaml files or by labels too.

Using labels we can decide on which node we have to create a POD, Generally scheduler assign or do placement of POD.
But we can use labels to attach POD to specific node.

Eg:
```
kubectl label pods testpod name=Mayank  :here we are assinging label to existing pod
kubectl get pods --show-label  : It will show all pods along with their labels
kubectl get pods -l name=Mayank   :It will give the pod having label as Mayank
kubectl delete pod -l  name=Mayank  : It will delete the pod having label as Mayank
```
```
#EXAMPLE OF LABELS

kind: Pod
apiVersion: v1
metadata:
  name: cloudsphere
  labels:                                                   
    env: development
    class: pods
spec:
    containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo Hello-Mayank; sleep 5 ; done"]

kubectl apply -f pod5.yml  ---> to execute yml file
kubectl get pods
kubectl get pods --show-labels   ---> to check all labels
kubectl label pods cloudsphere myname=Mayank  ---> to give label to existing pod
kubectl get pods -l env=development
kubectl get pods -l 'env in (development, testing)'

```

# Selectors

After assiging labels to any objects then to extract those objects we can use selectors.
First we have to give labels to object only after that we can use selectors to extract the information.
Eg:
  nodeSelector:
    hardware: 32 GB RAM

In above example, We are expecting those VMs which have 32GB RAM or who have tag with same name.

### Two types of selectors
1. Equality Type: eg name=Mayank
2. Set Based (in, notin, exists): eg env in (DEV, QA, PROD): Means it will give pod whose name or label is dev,qa or prod

Eg:
```
kubectl get pods -l env in (dev, qa)   -> -l is for label
kubectl get pods -l env notin (dev)

```
# Node-Selector
1. Generally scheduler assign or do placement of POD on a node.
2. We can use labels to tag the nodes and using node-selector we can choose or assign on which node we need to assign a pod specifying the label
3. Intially we have to assign a label and then use node-selector

```
# NODE SELECTOR EXAMPLE

kind: Pod
apiVersion: v1
metadata:
  name: nodelabels
  labels:
    env: development
spec:
    containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo Hello-Mayank; sleep 5 ; done"]
    nodeSelector:                                         
       hardware: t2-medium  ---> means create pod only on those node where hardware is t2-medium

kubectl apply -f pod6.yml
kubectl get pods
kubectl describe pod nodelabels
kubectl get nodes
kubectl label nodes <ip of node> hardware=t2-medium
kubectl describe pod nodelabels
kubectl get pods

```

# Important features of K8S
### Replica
It will create multiple pods with same features in the node. We used it to support high availbility means if one pod goes down due to any reason then second pod will work.

### Scaling
When load does become too much (traffic increase) for the number of existing pods then k8s will enables us to easily scaleup or scale down the application by adding or deleting the pods

### Reliability
By having multiple version of your application you can prevent application downsite if one pod fails

### Load Balancing
Multiple version of container also helps to balance the load if traffic increase, we can deviate the traffic to multiple contianers to avoid traffic

### Rollback
We can easily jump to any previous version in case of any bug on current application version


# Replication Controller
1. It a object that enables to create multiple pods and make sure that much pods are always available.
2. If a POD creates using RC then it will be automatic replaced if any pod crash.
3. RC is recommended if you just want to make sure 1 pod is always running even after system fails.
4. We can run RC with 1 replica and RC will make sure the pod is always running

Eg: kind: replication controller  -> it will recreate the pod in case of failure and make sure our application is running 

```
kubectl get rc : it will give those pods which are create using RC
kubectl scale --replicas=8 rc -l name=mayank  : it will create 8 replicas of same pods who have label as mayank

```
Disadvantage: RC support equality based selector.

```
#EXAMPLE OF REPLICATION CONTROLLER

kind: ReplicationController       # this define create object of RC type      
apiVersion: v1
metadata:
  name: myreplica
spec:
  replicas: 2          # desired number of pods  
  selector:            # tell controller if pod fails then create new one who have label as Mayank
    myname: Mayank                            
  template:                
    metadata:
      name: testpod6
      labels:            
        myname: Mayank  # label to pod, this must be same as selector
    spec:
     containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo Hello-Mayank; sleep 5 ; done"]

kubectl apply -f myrc.yml
kubectl get rc
kubectl describe rc myreplica
kubectl get pods
kubectl delete pod <podname>
kubectl get pods
kubectl scale --replicas=3 rc -l myname=Mayank  --> scale up from 2 to 3 pods who have label as Mayank

```
# Replica Set
It also provide same feature as RC but in this we can use set based selector along with equality based selector.

eg:
selector:
  env in (dev,qa)

```
# EXAMPLE OF REPLICA SET

kind: ReplicaSet                                    
apiVersion: apps/v1   ----> as in v1 it is not available so using apps/v1                         
metadata:
  name: myrs
spec:
  replicas: 2  
  selector:                  
    matchExpressions:                             # these must match the labels
      - {key: myname, operator: In, values: [Mayank, Ankit, Rahul]}
      - {key: env, operator: NotIn, values: [production]}
  template:      
    metadata:
      name: testpod7
      labels:              
        myname: Mayank
    spec:
     containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo Cloud Sphere; sleep 5 ; done"]

kubectl apply -f myrs.yml
kubectl get rs
kubectl get pods
kubectl scale --replicas=3 rs/myrs ---> scaleup

```
# Deployment
RC and RS  is not able to do updates and rollbacks in the cluster.

Deployments are upgraded and higher version of replication controller. They manage the deployment of replica sets which is also an upgraded version of the replication controller. They have the capability to update the replica set 
and are also capable of rolling back to the previous version.

During the rollback number of pods will remain same

If there are any problems in the deployment, k8s will automatically rollback to the previous version. However we can specifically rollout to any revision/version. 

To rollback to specific version use : --to-revision
kubectl rollout undo deploy/mydeployments --to-revision=2

Kubectl get deploy -> when you run this commands following fields will effect
1. NAME: list of deployments
2. READY: Display how many replicas of the application are available to users. (ready/desired)
3. UP-TO-DATE: display no of replicas that have been updated to achieve the desired state
4. AVAILABLE: display how many replicas of the application are available
5. AGE: amount of time that the applciation has been running

```
# deployment 

kind: Deployment
apiVersion: apps/v1
metadata:
   name: mydeployments
spec:
   replicas: 2
   selector:     
    matchLabels:
     name: deployment
   template:
     metadata:
       name: testpod
       labels:
         name: deployment
     spec:
      containers:
        - name: c00
          image: ubuntu
          command: ["/bin/bash", "-c", "while true; do echo Mayank; sleep 5; done"]

kubectl apply -f mydeploy.yml
kubectl get deploy
kubectl describe deploy mydeployments
kubectl get rs
kubectl get pods
kubectl logs -f <podname>
kubectl rollout status deployment <deploymentname>  
kubectl rollout history deployment <deploymentname> 

```
### Changing the Deployment
1. Updating − The user can update the ongoing deployment before it is completed. In this, the existing deployment will be settled and new deployment will be created.
2. Deleting − The user can pause/cancel the deployment by deleting it before it is completed. Recreating the same deployment will resume it.
3. Rollback − We can roll back the deployment or the deployment in progress

### Deployment Strategies
Deployment strategies help in defining how the new RC should replace the existing RC.
1. Recreate − This feature will kill all the existing RC and then bring up the new ones. This results in quick deployment however it will result in downtime when the old pods are down and the new pods have not come up.
2. Rolling Update − This feature gradually brings down the old RC and brings up the new one. This results in slow deployment, however there is no deployment.

### Reasons for failed deployments
1. image pull errors
2. Insufficient permissions
3. Application runtime config issue

# Networking
K8S networking addresses four concern:
1. Containers within a pod can communicate using localhost.
2. Cluster networking provide communication between different pod
3. The service  resources let you expose our application running in pod to outside our cluser.

```
# Networking between 2 container on same pod

kind: Pod
apiVersion: v1
metadata:
  name: testpod
spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Hello-Mayank; sleep 5 ; done"]
    - name: c01
      image: httpd
      ports:
       - containerPort: 80

kubectl apply -f pod.yml
kubectl get pods
kubectl exec testpod -it - c c00 -- /bin/bash  --> c: container
apt update && apt install curl
curl localhost:80

```

```
# Networking between 2 pod on same node

kind: Pod
apiVersion: v1
metadata:
  name: testpod1
spec:
  containers:
    - name: c01
      image: nginx
      command: ["/bin/bash", "-c", "while true; do echo Hello-Mayank; sleep 5 ; done"]
      ports:
       - containerPort: 80

kind: Pod
apiVersion: v1
metadata:
  name: testpod2
spec:
  containers:
    - name: c03
      image: httpd
      command: ["/bin/bash", "-c", "while true; do echo Hello-Mayank; sleep 5 ; done"]
      ports:
       - containerPort: 80

kubectl apply -f pod1.yml
kubectl apply -f pod2.yml
kubectl get pods -o wide
apt update && apt install curl
curl <Pod1IP>:80
curl <Pod2IP>:80

```

# Service

Each pod have its own IP, however in a deployment the set of pods running in one moment of time could be different from the set of pods running that application a moment later.
This leads to problem:
Lets' says if some set of pods(backend) provides functionality to some another pod(frontend) but those set of pods changes then IP will also change thus leading to application not found issue. As frontend pods does not know about the backend new IP or vice versa.

To overcome this we use Services
Service provide virtual IP to object (replica set, RC, deployment etc) so that virtual IP of object will map to pods

- When using RC , pods are terminated and created during scale up and scale down there we use service
- When using deployments, while updating the image version the pods are terminated there we can use it
- Service is a logical bridge between pods and end users which provide virtual IP
- Service allows clients to connect to the containers running in the POD using VIP
- Kube proxy is the one which keeps the mapping between the VIP and the pods upto date. 
- Service helps to expose the VIP mapped to the pods & allows application to recieve traffic
- Labels are used to select which are the pods to be put under service

### Services are exposed in different ways
1. Cluster IP (default)
2. Nodeport
3. Loadbalancer: provided by cloud 
By default, service can run only between 30000-32767 ports

### Cluster IP
- To communicate between pod but in different node
- Exposed VIP reachable only in cluster itself.
- Mainly used to communicate between different microservice of application

```
kind: Deployment
apiVersion: apps/v1
metadata:
   name: mydeployments
spec:
   replicas: 1
   selector:      # tells the controller which pods to watch/belong to
    matchLabels:
     name: deployment
   template:
     metadata:
       name: testpod1
       labels:
         name: deployment
     spec:
      containers:
        - name: c00
          image: httpd
          ports:
          - containerPort: 80
kubectl apply -f pod1.yml
kubctl get pods
kubectl get pods -o wide
curl <PODIP>:80
```

```
kind: Service                             # Defines to create Service type Object
apiVersion: v1
metadata:
  name: demoservice
spec:
  ports:
    - port: 80                               # Containers port exposed
      targetPort: 80                     # Pods port
  selector:
    name: deployment                    # Apply this service to any pods which has the specific label
  type: ClusterIP                       # Specifies the service type i.e ClusterIP or NodePort

kubectl apply -f pod2.yml
kubectl get svc --> provide cluster IP
kubectl delete pod <podname>
kubectl get pods
curl <clusterIP>:80

```

### NodePort
- Makes a service accessible from outside the cluser
- It map VIP to the cluster instead of object

```
Use same file used for clusterIP
kubectl apply -f pod2.yml
In service yml rename ClusterIP to nodeport
kubectl get svc
Copy public DNS from AWS and hit the URL:port

```
# Volume
Container are short lived in nature, all the data stored inside a container is deleted if container crashes.

However kubelet will restart it with clean state means all the old data will clean or deleted. To overcome this issue k8s offers volume.

Volume is attached to a POD and shared among the containers of that POD. Volume has same life cycle as of POD.

In Kubernetes, a volume can be thought of as a directory which is accessible to the containers in a pod. We have different types of volumes in Kubernetes and the type defines how the volume is created and its content.

The concept of volume was present with the Docker, however the only issue was that the volume was very much limited to a particular pod. As soon as the life of a pod ended, the volume was also lost.

On the other hand, the volumes that are created through Kubernetes is not limited to any container. It supports any or all the containers deployed inside the pod of Kubernetes. A key advantage of Kubernetes volume is, it supports different kind of storage wherein the pod can use multiple of them at the same time.

All the data store in the container will be deleted if container crash or terminate after completeion of task which leads to lost of information present in the volume or storage which container have to avoid this we use k8s volumes.

Here, if container crash then volume will not deleted it will automatically map to new container. Volume can be shared with multiple container within same POD.

The life of volume is same as POD, if pod delete the volume also delete but not depend on container life

# Types of Kubernetes Volume
Volume types decide the nature/properties of directory like size, content etc.

### emptydir: 
1. We use this when we want to share content with multiple container on same POD not on local host(VM)
2. Emptydir created first when pod is assigned to node and it is intially empty.
3. If container crash then it will not effect the data as it is associated with POD.
4. Once the Pod is removed from the node, the data in the emptyDir is erased.
5. Volumes can be mounted in same or different path in the containers inside POD

```
apiVersion: v1
kind: Pod
metadata:
  name: myvolemptydir
spec:
  containers:
  - name: c1
    image: centos
    command: ["/bin/bash", "-c", "sleep 15000"]
    volumeMounts:                                    # Mount definition inside the container
      - name: xchange
        mountPath: "/tmp/xchange"          
  - name: c2
    image: centos
    command: ["/bin/bash", "-c", "sleep 10000"]
    volumeMounts:
      - name: xchange
        mountPath: "/tmp/data"
  volumes:                                                   
  - name: xchange
    emptyDir: {}

kubectl apply -f pod.yml
kubectl get pods
kubectl exec myvolemptydir -c c1 -it -- /bin/bash
cd /tmp
ls
cd xchange/
vi abc.yml
cloudsphere
:wq
exit
kubectl exec myvolemptydir -c c2 -it -- /bin/bash
cd /tmp
cd data/
ls  ---> here abc.yml will be available in container 2, as volume is shared for both container
```

### hostPath
We use this when we want to access pod/container data from local machine (VM)
eg:
Volumes:
  -name:test
  hostpath:
    path: /temp/data  -> location of local machine

```
apiVersion: v1
kind: Pod
metadata:
  name: myvolhostpath
spec:
  containers:
  - image: centos
    name: testc
    command: ["/bin/bash", "-c", "sleep 15000"]
    volumeMounts:
    - mountPath: /tmp/hostpath
      name: testvolume
  volumes:
  - name: testvolume
    hostPath:
      path: /tmp/data 

kubectl apply -f pod.yml   
ls /tmp
cd /tmp/data
kubectl exec myvolhostpath --ls /tmp/hospath
echo "cloudsphere" >>myfile
kubectl exec myvolhostpath --ls /tmp/hospath
```
### gitRepo
A gitRepo volume mounts an empty directory and clones a git repository into it for your pod to use.

### azureDiskVolume 
An AzureDiskVolume is used to mount a Microsoft Azure Data Disk into a Pod.

# Persistent Volume
Why: In case of simple volume like emptydir,hostpath data can be easily access if container crash as data is sync with POD. But what if POD crash or POD created on some another node in this case we can lost data.
To avoid this situation we have PV.

PV is link to node instead of POD. If pod got deleted then our storege still presist. As PV is directly connected with node.

PV is just like a shared volume (dropbox, google drive).
In azure we have storage accounts(blob, azure table) which is PV. Whatever the reason for pod failure data will never lost

### Persistent Volume Claim (PVC)
To use PV we need to clain it first using PVC.

PVC request a persistent volume with our requirements (size,speed etc) and after acknowledgement we can mount those to our pod and use the volume

After successful bound to a pod, you can mount it as volume

The storage requested by Kubernetes for its pods is known as PVC. The user does not need to know the underlying provisioning. The claims must be created in the same namespace where the pod is created.

Also the instance/VM should be in same region/availabity zone as volume

### Creating Persistent Volume
```
kind: PersistentVolume ---------> kind as PV which tells k8s  that the yaml file being used is to create the PV.
apiVersion: v1
metadata:
   name: pv0001 ------------------> Name of PersistentVolume that we are creating.
   labels:
      type: local
spec:
   capacity: -----------------------> This spec will define the capacity of PV that we are trying to create.
      storage: 10Gi
   accessModes:
      - ReadWriteOnce -------------------> This tells the access rights of the volume that we are creating.
      hostPath:
         path: "/tmp/data01" --------------------------> (VM and PV(azurestorage) should be in same zone)

$ kubectl create –f local-01.yaml  -----> PV will created
$ kubectl get pv  ----> to check PV

```

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: myebsvol
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle   # means we can resuse same volume
  awsElasticBlockStore:
    volumeID:           # ID of your EBS volume
    fsType: ext4

$ kubectl create –f local-01.yaml  -----> PV will created
$ kubectl get pv  ----> to check PV

```
### Creating Persistent Volume Claim
```
kind: PersistentVolumeClaim -> It instructs the infrastructure that we are trying to claim a this amount of space.
apiVersion: v1
metadata:
   name: myclaim-1 --------------------> Name of the claim that we are trying to create.
spec:
   accessModes:
      - ReadWriteOnce -------------> This specifies the mode of the claim that we are trying to create.
   resources:
      requests:
         storage: 3Gi -----------> This will tell kubernetes about the amount of space we are trying to claim.

kubectl create –f myclaim-1  ----> creating PVC
$ kubectl get pvc ---> getting details of PVC

```

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myebsvolclaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

kubectl create –f myclaim-1.yml  ----> creating PVC
$ kubectl get pvc ---> getting details of PVC

```
### Using PV and PVC with POD

```
kind: Pod
apiVersion: v1
metadata:
   name: mypod
   labels:
      name: frontendhttp
spec:
   containers:
   - name: myfrontend
      image: nginx
      ports:
      - containerPort: 80
         name: "http-server"
      volumeMounts: -----------> This is the path in the container on which the mounting will take place.
      - mountPath: "/usr/share/tomcat/html"
         name: mypd
   volumes: ---------> This definition defines the volume definition that we are going to claim.
      - name: mypd
         persistentVolumeClaim: -------> we define the volume name which we are going to use in the defined pod.
         claimName: myclaim-1

```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pvdeploy
spec:
  replicas: 1
  selector:      # tells the controller which pods to watch/belong to
    matchLabels:
     app: mypv
  template:
    metadata:
      labels:
        app: mypv
    spec:
      containers:
      - name: shell
        image: centos
        command: ["bin/bash", "-c", "sleep 10000"]
        volumeMounts:
        - name: mypd
          mountPath: "/tmp/persistent"
      volumes:
        - name: mypd
          persistentVolumeClaim:
            claimName: myebsvolclaim

kubectl create –f probe.yml

```

# HealthCheck/LivenessProbe
1. K8s only insure that pod or container is running but does not care about the application in the container whether it is running or not so check that we have livenessProbe
2. LivenessProbe means we will tell the probe to run certain commands in the container to check whether our applications is running well or not
3. If the probe output is zero means application is running fine otherwise not.
4. For running healthchecks we will use specfic commands related to the application.

```
# Liveness Probe

apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: mylivenessprobe
spec:
  containers:
  - name: liveness
    image: ubuntu
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 1000  # touch command is used to create a file
    livenessProbe:                                          
      exec:
        command:                                         
        - cat                
        - /tmp/healthy
      initialDelaySeconds: 5          # to run the check after given time
      periodSeconds: 5        # time period between health check, after every 5 sec healthcheck run                
      timeoutSeconds: 30    # wait for the output till given time                           

kubectl describe pod <podname>
kubectl exec <podname> -it -- /bin/bash
cat /tmp/healthy
echo $?  # to check the healthcheck result

```

# ConfigMap
1. While performing apllication deployment on k8s cluster sometimes we need to change the application configuration file depends on the env like dev, qa or prod.
2. In normal scenerio changing the config file means changing,commiting,push the code and then create new image which is not efficient way in automation point of view.
3. So the config files should be decouples to keep the process smooth and automate
4. To make this happen we use config map 
5. These are useful to store and share non-sensitive information which will be required in each env.
6. Configmap are attached to cluster hence all worker node can use it.

### Config map can be access in 2 ways
- Env variables: here we can only read the content
- Volume in pods: store config file in volume and each pod can access it

kubectl create configmap<name> --from-file=<configfilename>

# Secrets
1. It is used to store sensitive information which can be required in each env like userID, passwork, keyvault password
2. It can access via volume or env variable from a container running in a pod
3. Config map or secret should be max of 1MB in size
4. The secret data on node will stored in tmfs volume(tmfs is file system which keeps all file in virtual memory so no file should be create in hard disk)
5. Secret can be created from text file or yaml file

```
#config map
create a sample conf file
v1 sample.conf
kubectl create configmap mymap --from-file=sample.cong
kubectl get configmap
kubectl describe configmap mymap

apiVersion: v1
kind: Pod
metadata:
  name: myvolconfig
spec:
  containers:
  - name: c1
    image: centos
    command: ["/bin/bash", "-c", "while true; do echo Technical-Guftgu; sleep 5 ; done"]
    volumeMounts:
      - name: testconfigmap
        mountPath: "/tmp/config"   # the config files will be mounted as ReadOnly by default here
  volumes:
  - name: testconfigmap
    configMap:
       name: mymap   # this should match the config map name created in the first step
       items:
       - key: sample.conf
         path: sample.conf

```
```
create a file
echo "root" > username.txt; echo "password123" > password.txt
cat username.txt ---> to check content
kubectl create generic mysecret --from-file=username.txt --from-file=password.txt ---> to create a secret
kubectl get secret
kubectl descibe secret mysecret

# secret
apiVersion: v1
kind: Pod
metadata:
  name: myvolsecret
spec:
  containers:
  - name: c1
    image: centos
    command: ["/bin/bash", "-c", "while true; do echo Technical-guftgu; sleep 5 ; done"]
    volumeMounts:
      - name: testsecret
        mountPath: "/tmp/mysecrets"   # the secret files will be mounted as ReadOnly by default here
  volumes:
  - name: testsecret
    secret:
       secretName: mysecret  

Here container can access the secret value but not any hostVM

```

# Namespace
All the objects like pods, deployment, RC are created in default namespace means multiple application run in single namespace but in real IT world we have many projects and applications to remember all their objects like pods, RC are difficult to remember. So to segragete the objects of respective application we can use namespace

A namespace is a group of related elements/objects which have unique name and specific to single application.

Eg: College is cluster
    Branch is namespace which having all related elements/objects like HOD, teacher etc

By default k8s having a namespace and run all applications in it. We can also use resource quota and specify how many resource each namespace can have.

Most k8s resources like pods, deployment, RS, RC are in some namespace except PV and nodes.

Eg:
- Let us assume we have shared k8s cluster for dev and prod.
- The dev team would like to maintain a space in the cluster where they can get a view on the list of pode, services and deployment they used to build and run their application. In this no restrictions are put on who can or cannot modify the resources in the cluser
- But in PROD, we need to put some resctriction so that anyone can modify the reosurces. 

So in this usecase we can have 2 namespace one for dev and one for prod in same cluster but having different policies, resources based on requirements and needs.

```
kubectl get namespace # to view namespace

vi namespcae.yml
apiVersion: v1
kind: Namespace
metadata:
   name: dev
   labels:
     name: dev

kubectl apply -f namespace.yml
kubectl get namespace # to view namespace

# to create a pod

vi pod.yml

kind: Pod                              
apiVersion: v1                     
metadata:                           
  name: testpod                  
spec:                                    
  containers:                      
    - name: c00                     
      image: ubuntu              
      command: ["/bin/bash", "-c", "while true; do echo Technical Guftgu; sleep 5 ; done"]
  restartPolicy: Never   

kubectl apply -f pod.yml -n dev  # this will create pod in dev namespace not in default
kubectl delete -f pod.yml -n dev   # this will delete pod in dev namespace not in default

kubectl config set-context $(kubectl config current-context) --namespace=dev  # this will create dev namepace default namespace

kubectl get pods
kubectl config view | grep namespace:  # this will show in which namespace we are working

```

# Horizontal Pod Autoscaler
K8S have possibility to automatically scales pods based on CPU utilization which is HPA. Scaling can be done only for scalable objects like deployment, RS, RC etc.

The controller periodically adjust the number of replica in RC or deployment to match current CPU utilization to target specified.

We deploy 3rd party API in cluster i.e. metric-server which is used to collect data or metrics of CPU utilization and then inform HPA. The HPA will check CPU utilization after every 30 seconds.

There is a cooldown period which is used to wait bedore deleting any POD to downscale. Means if CPU thresold is less then specified thresold value then controller wait for 5 mins before deleting any POD

--kubelet-insecure-tls ----> it will bypass all required certificate
kubectl autoscale deployment mydeploy --cpu-percent=20 --min=1 --max=10 ---> to create HPA\
--cpu-percent=20 ---> thresold value\
--min=1 ----> it will keep minimum 1 pod not less then that\
--max=10 ----> it will scale up upto 10 pod

```
# download metric server:
$ wget -O metricserver.yml https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbDRCb0xGVTlJS2RRM1JTQWxaV3l0ZGlyMWdrZ3xBQ3Jtc0tsUTdMQnFXMzdTUzdPNmNvaHN6b0hjRVZ2MS1DSWxRUGg1ZE5FQ1lXcW5XU1lhMHNWZlU0ZjFqWGswWENiUDQ1NFZlblZOLUlDMS12eUlsbm15MEFqZUtEVi1jdktHWjBHSnNEQUMweEVVREdUajROMA&q=https%3A%2F%2Fgithub.com%2Fkubernetes-sigs%2Fmetrics-server%2Freleases%2Flatest%2Fdownload%2Fcomponents.yaml&v=hm3jnETOoFo

kind: Deployment
apiVersion: apps/v1
metadata:
   name: mydeploy
spec:
   replicas: 1
   selector:
    matchLabels:
     name: deployment
   template:
     metadata:
       name: testpod8
       labels:
         name: deployment
     spec:
      containers:
        - name: c00
          image: httpd
          ports:
          - containerPort: 80
          resources:
            limits:
              cpu: 500m
            requests:
              cpu: 200m

kubectl autoscale deployment mydeploy --cpu-percent=20 --min=1 --max=10 ---> to create HPA
```
