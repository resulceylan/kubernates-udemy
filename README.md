https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/
####Setting up Ingress with Docker Desktop's Kubernetes
* kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
* kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/cloud-generic.yaml
* kubectl get svc -n ingress-nginx
# Kubernetes
* Kubernetes: System for running many different containers over multiple different machines
* When you need to run many different containers with different images
* Each node is a virtual machine or physical computer
* Master controls what each node does
* Nodes + master form a _Cluster_
* minikube managing VM itself (local only)
* kubectl managing containers in the node
* each apiVersion defines a different set of 'Object' we can use
* containers run inside Pods
* We put containers that are very tightly coupled in the same pod (like postgres + backup manager, if postgres container crashes, back up manager container will be useless, like so)
## Services
* Services, set up networking in a k8s cluster

* In pod definition you define component: web under labels. Then in client-node-port, you find that pod with selector by its label component: web
### NodePort
* NodePort service (shouldn't be used in prod)

* port: the port which other containers inside cluster can reach that specific container

* targetPort: the port that specific container opened outside to be reached out, like 8080 port of a tomcat container
* nodePort: a random port required to reach that specific container outside the cluster, like http://localhost 31515. should be between 30000-32767
### LoadBalancer
* Legacy way of getting network traffic into cluster
### Ingress
* Exposes a set of services to the outside world 
### ClusterIP
* Exposes a set of pods to other objects in the cluster
* kubectl apply -f ....yml
* kubectl get pods
* kubectl get services
* name (client-pod) and kind (pod) are unique identifier tokens for objects. So do not change them.
* kubectl describe <object-type> <object-name> => gets detailed information about object
* 'Deployment' is a k8s object that maintains a set of identical pods, ensuring that they have the correct config and that the right number exist
* Pod: runs single set of container, good for one-off dev purposes, rarely used directly in prod
*  Deployment: runs a set of identical pods, monitors the state of each pod (updating when necessary), good for prod and dev
* Deployment config file contains a template of a pod which will be created
* kubectl delete -f <config-file>
* When a docker image changes, since k8s config didn't change, the image inside k8s isn't updated. To solve this,  you may tag then image with version and use it in config. >kubectl set image <object-type>/<object-name> <container-name>=<new image to use> => kubectl set image deployment/ client-deployment client=stephengrider/multi-client (updates a property in k8s in this case the image)
### Volumes
* Volumes in k8s an object that allows a container to store data at the pod level. 'Volume' is created inside a pod. If smth happens to pod, you will loose data
* 'Persistent volume' is created outside pod. Independent of any pod. If smth happens to pod, you will not loose data
* 'Persistent Volume Claim' is an advertisement actually. It is not a volume actually.
* Statically provisioned persistent volume (ready to use, provisioned previously before demand)
* Dyamically provisioned persistent volume (not ready to use, provisioned on the fly on demand)
#### Access modes
* ReadWriteOnce can be used by single node
* ReadOnlyMany Multiple nodes can read only
* ReadWriteMany can be accessed by many nodes
* under volumeMounts, it says find a volume with name postgres-storage. It goes to volumes section and allocates a volume specified with 'database-persistent-volume-claim'. mountPath is which directory inside the container to be mapped (/var/lib/postgres/data).  subPath is the value in which directory inside persisted volume will be mapped
* kubectl get pv
* kubectl get pvc
### Secret
* Securely stores a piece of information in the cluster such as a database password
* kubectl create secret generic pgpassword (this is the name of the secret to be referenced inside k8s) --from-literal PGPASSWORD=postgres
* kubectl get secrets