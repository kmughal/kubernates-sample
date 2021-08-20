# Introduction

This is a sample read me for Kubernates.

## Setup Kubernates in mac

- install kubectrl : ``` brew install kubectl ```
- install minikube: ``` brew install minikube ```
- install hyperkit: ``` brew install hyperkit ```
- start minikube: ``` minikube start --vm-driver=hyperkit ```
- test minikube status: ``` minikube status ```
- test kubectrl: ``` kubectl version ```
- test node status: ``` kubectl get nodes ```
  
## Setup PODS

Pods is the smallest unit and you can not start it directly. This is where another abstraction comes into picture which is Deployment!

```
kubectl create deployment NAME --image=image 
```

- lets say if you want to create a deployment for nginx! ``` kubectl create deployment ngix-sample --image=nginx ```
- in order to verify this : ``` kubectl get deployment ``` -> you will see the nginx deployment
- in order to see pods : ``` kubectl get pods ``` -> you will see the pod!
- between deployment and pods there is another layer called Replicaset in order to see that ``` kubectl get replicaset ```

*** Summary ***
deployment -> manages replicaset -> manages replicaet of all pods -> pod is an abstraction over container
- in order to edit the deployment image use ``` kubectl edit deployment ngix-sample -> this will open the editor for you to edit the config!
- to view the logs ``` kubectl logs ngix-sample-5b4c97b5b5-8x9kg ```
- to get some additional information ``` kubectl describe pod ngix-sample-5b4c97b5b5-8x9kg ```
- lets say we want to setup mongo pod ``` kubectl create deployment my-mongo-instance --image=mongo ``` followed by ``` kubectl get pods ``` if you think it is not running you can get extra information by ``` kubectl describe pod <<pod-hash>> ``` this will give you detailed information! then to view the log for the mongo db use ``` kubectl logs <<hash>> ```
- another very important command is ``` kubectl exec -it mongo-db-6ccb7d655-gjvhq -- bin/bash ``` this will take you inside the pod!
- in order to get rid of pod you need to delete the deployment ``` kubectl delete deployment mongo-db ```
- you can use ``` kubectl apply -f <<yml-file>> ``` to take settings from a config file .e.g.
  
  ``` kubctl apply -f resources/ng-service.yaml ``` and ``` kubctl apply -f resources/ng-deployment.yaml ``` will create pods and services.

  - in order to verify the service run the following command.

```

kubectl describe service nginx-service
Name:              nginx-service
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          name=nginx
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.105.186.33
IPs:               10.105.186.33
Port:              <unset>  80/TCP
TargetPort:        8080/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>

```

- in order to get some more information use 

```
 kubectl get pod -o wide

NAME                               READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
nginx-deployment-f4b7bbcbc-nfzrw   1/1     Running   0          5m56s   172.17.0.3   minikube   <none>           <none>
nginx-deployment-f4b7bbcbc-spkd2   1/1     Running   0          5m56s   172.17.0.4   minikube   <none>           <none>
```

- to get the status see below:

```

 kubectl get deployment nginx-deployment -o yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"default"},"spec":{"replicas":2,"selector":{"matchLabels":{"app":"nginx"}},"template":{"metadata":{"labels":{"app":"nginx"}},"spec":{"containers":[{"image":"nginx:1.16","name":"nginx","ports":[{"containerPort":8080}]}]}}}}
  creationTimestamp: "2021-08-15T18:20:02Z"
  generation: 1
  name: nginx-deployment
  namespace: default
  resourceVersion: "3032"
  uid: 7d6afe13-9e96-46e4-bc0f-51efe5486ab1
spec:
  progressDeadlineSeconds: 600
  replicas: 2
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nginx
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.16
        imagePullPolicy: IfNotPresent
        name: nginx
        ports:
        - containerPort: 8080
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 2
  conditions:
  - lastTransitionTime: "2021-08-15T18:20:20Z"
    lastUpdateTime: "2021-08-15T18:20:20Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2021-08-15T18:20:02Z"
    lastUpdateTime: "2021-08-15T18:20:20Z"
    message: ReplicaSet "nginx-deployment-f4b7bbcbc" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 2
  replicas: 2
  updatedReplicas: 2


```


- in order to delete services and deployment use this :

```

kubectl delete -f resources/ng-deployment.yaml 
deployment.apps "nginx-deployment" deleted

 kubectl delete -f resources/ng-service.yaml   
service "nginx-service" deleted

```

- to get all info of kubectl:  ``` kubectl get all ```

### Example

#### Mongo db Pods and Service

- fisrt we need to create and apply secrets for that use this command : ```  kubectl apply -f mongo-secret.yaml ```
 once secrets are applied then we can use those in the mongo deployment! running following command : 
 ```

 kubectl get secret
NAME                  TYPE                                  DATA   AGE
default-token-9hmjj   kubernetes.io/service-account-token   3      6h56m
mongo-db-secret       opaque                                2      100s

```
- now deploy the mongo deployment by using ``` kubectl apply -f mongo-deployment.yaml 
deployment.apps/mongodb-deployment created ```


- in the mongo-express file under the service you have specified the type as LoadBalance you need an external ip address for that as we are using minikube which work differently. In order to see things ... use the following command 

```

 minikube service mongo-express-service
|-----------|-----------------------|-------------|---------------------------|
| NAMESPACE |         NAME          | TARGET PORT |            URL            |
|-----------|-----------------------|-------------|---------------------------|
| default   | mongo-express-service |        8081 | http://192.168.64.2:30000 |
|-----------|-----------------------|-------------|---------------------------|
🎉  Opening service default/mongo-express-service in default browser...

```

- in order to enable ingress controller please use following command :
  ```  minikube addons enable ingress ``` in order to verify the installation ``` kubectl get pod -n kube-system ```


  ### Ingress sample for Kubernates Dashboard

  - enable kubernates dashboard locally: ``` minikube dashboard ```
  - verify ```  kubectl get all -n kubernetes-dashboard ``` the pod and service is up
  - once you apply the ingress file to check : ```kubectl get ingress  -n kubernetes-dashboard ```
  - you need to change the host file ```  sudo vim /etc/hosts ```


## Helm

It is a package manager for kubernates. This enables you to package yaml files and distribute to private and public repos.

### Helm Chart

- Yaml files which are packaged together is referred as Helm chart.
- Helm 2 has Client and Server(which is called Tiller). So when you type helm install <chartname> => this is send to the Server (Tiller).
- Helm 3 the Tiller is removed due to security and control issue.

### Kubernates Volume

- persistent volume => folder where you store files
- persistent volume claim
- storage claim

admin creates volumes and user claim those volume!
So in short 

- volume is a directory with some data.
- volumes are accessible in container in a pod.
- storage class automates the process of creating volumes which can be later on claimed. For storage class you set up the yaml file where you tell about which provisiner to use which are internal to kubernates.

### Stateful set and Stateful application

- old databases -> posgres , sql , elastic search
- stateless applications are deployed using deployments
- stateful applications are deployed using statefulSet.

### Kubernates Services

- ClusterIP Service
- NodePort Service
- Headless Service
- LoadBalancer Service
