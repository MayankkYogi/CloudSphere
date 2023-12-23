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
K8S: Complicated and series of manual steps
Swarm: Fast and easy

### Supports
K8S: It can work with any type of container docker, rocket
Swarm: Support only docker container

### GUI
K8S: No GUI
Swarm: GUI available

### Volume
K8S: Only shared with container inside same POD
Swarm: Can be shared with any containers

### Auto Scaling
K8S: Support Vertical and horizontal
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
- Luanch 3 instances (one master node and two worker node)
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
3. Also fove information about the policies like restart policies, fault tolerance etc.
4. After creating objects k8s make sure that system will constantly work to esure that object exist and maintain desired cluster state

### Types of objects:
- POD
- Service
- Volume
- Secrets
- Job
- Deployment
- Replica Sets
- Namespace

# Minikube
It is a lightweight k8s implementation that creates a VM on your local machine and deploy a simple cluster containing only one node

### kubectl
It is a k8s command line tool. It allows to run command against your clusters. We can use this to deploy, inspect and manager cluster and also view logs.

# Labels
Labels are mechanism which we can use to organise the kubernetes objects. This labels we can apply on node, pod or any objects. It is similar to tag which we used in Azure.

### Why Labels
Suppose there are 100 containers of different project and we only want a specific project cluster then we can use lables to get the desired results.

Labels are key-value pair without any predefined meaning. Multiple labels can be added to single objects. We can also delete pod using pod name, yaml files or by labels too.

Using labels we can decide on which node we have to create a POD, Generally schedules assign or do placement of POD.
But we can use labels to attach POD to specific node.

Eg:
```
kubectl label pod testpod name=Mayank  :here we are assinging label to existing pod
kubectl get pods --show-label  : It will show all pods along with their labels
kubectl get pods -l name=Mayank   :It will give the pod having label as Mayank
kubectl delete pod -l  name=Mayank  : It will delete the pod having label as Mayank
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
2. Set Based: eg env in (DEV, QA, PROD): Means it will give pod whose name or label is dev,qa or prod

Eg:
```
kubectl get pods -l env in (dev, qa)
kubectl get pods -l env notin (dev)
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
1. It a object that enables to create multiple pods and make sure that much pods are always available
2. If a POD creates using RC then it will be automatic replaced if any pod crash

Eg: kind: replication controller  -> it will recreate the pod in case of failure and make sure our application is running 
```
kubectl get rc : it will give those pods which are create using RC
kubectl scale --replicas=8 rc -l name=mayank  : it will create 8 replicas of same pods who have label as mayank

```
Disadvantage: RC support equality based selector.

# Replica Set
It also provide same feature as RC but in this we can use set based selector along with equality based

eg:
selector:
  env in (dev,qa)

# Deployment
Deployments are upgraded and higher version of replication controller. They manage the deployment of replica sets which is also an upgraded version of the replication controller. They have the capability to update the replica set and are also capable of rolling back to the previous version.

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

# Volume
In Kubernetes, a volume can be thought of as a directory which is accessible to the containers in a pod. We have different types of volumes in Kubernetes and the type defines how the volume is created and its content.

The concept of volume was present with the Docker, however the only issue was that the volume was very much limited to a particular pod. As soon as the life of a pod ended, the volume was also lost.

On the other hand, the volumes that are created through Kubernetes is not limited to any container. It supports any or all the containers deployed inside the pod of Kubernetes. A key advantage of Kubernetes volume is, it supports different kind of storage wherein the pod can use multiple of them at the same time.

All the data store in the container will be deleted if container crash or terminate after completeion of task which leads to lost of information present in the volume or storage which container have to avoid this we use k8s volumes.

Here, if container crash then volume will not deleted it will automatically map to new container. Volume can be shared with multiple container within same POD.

The life of volume is same as POD, if pod delete the volume also delete but not depend on container life

# Types of Kubernetes Volume
### emptydir: 
1. We use this when we want to share content with multiple container on same POD not on local host(VM)
2. Emptydir created first when pod is assigned to node and it is intially empty.
3. If container crash then it will not effect the data as it is associated with POD.
4. Once the Pod is removed from the node, the data in the emptyDir is erased.

### hostPath
We use this when we want to access pod/container data from local machine (VM)
eg:
Volumes:
  -name:test
  hostpath:
    path: /temp/data  -> location of local machine

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

The storage requested by Kubernetes for its pods is known as PVC. The user does not need to know the underlying provisioning. The claims must be created in the same namespace where the pod is created.

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
