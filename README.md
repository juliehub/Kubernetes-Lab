# Kubernetes-Lab

[kubectl CheatSheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
[kubectl Usage Conventions](https://kubernetes.io/docs/reference/kubectl/conventions/)

## Basics
#### Lab 1: PODs
See details of this lab via [Pods](https://github.com/juliehub/Kubernetes-Lab/blob/master/Lab1-Pods.md)
##### Important Commands for lab 1:
  - Download [pod-definition.yml](https://github.com/juliehub/Kubernetes-Lab/blob/master/pod-definition.yml)
  - Run commands:
  ```bash
  $ kubectl create -f pod-definition.yml
  $ kubectl get pods
  $ kubectl describe pod newpods-4rtwk 
  $ kubectl get pods -o wide
  $ kubectl delete pod webapp
  $ kubectl run redis --image=redis123 --dry-run=client -o yaml > pod.yaml
  $ kubectl apply -f pod.yaml
  $ kubectl edit pod redis
  ```
#### Lab 2: ReplicaSet
See details of this lab via [ReplicaSets](https://github.com/juliehub/Kubernetes-Lab/blob/master/Lab2-ReplicaSets.md)
##### Important Commands for lab 2:
  - Download [replicaset-definition.yml](https://github.com/juliehub/Kubernetes-Lab/blob/master/replicaset-definition.yml)
  - Run commands:
  ```bash
  $ kubectl create -f replicaset-definition.yml
  $ kubectl get replicatset
  $ kubectl get pods
  $ kubectl get all
  $ kubectl delete replicaset myapp-replicaset
  ```
  Scale with 6 replicas
  ```bash
  $ kubectl replace -f replicaset-definition.yml
  $ kubectl scale --replicas=6 -f replicaset-definition.yml
  $ kubectl scale --replicas=6 replicaset myapp-replicaset
  ```
#### Lab 3: Deployments
See details of this lab via [Deployments](https://github.com/juliehub/Kubernetes-Lab/blob/master/Lab3-Deployments.md)
##### Important Commands for lab 3:
  - Download [deployment-definition.yml](https://github.com/juliehub/Kubernetes-Lab/blob/master/deployment-definition.yml)
  - Run commands:
  ```bash
  $ kubectl create -f deployment-definition.yml
  $ kubectl get deployments
  $ kubectl get replicatset
  $ kubectl get pods
  $ kubectl get all
  ```






