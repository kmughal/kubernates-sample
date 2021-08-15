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
- you can use ``` kubectl apply -f <<yml-file>> ``` to take settings from a config file!