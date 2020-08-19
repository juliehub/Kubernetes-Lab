# Kubernetes-Lab

[CheatSheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

### Basics
- Lab 1: [Pods](https://github.com/juliehub/Kubernetes-Lab/blob/master/Lab1-Pods.md)
- Lab 2: [ReplicaSets](https://github.com/juliehub/Kubernetes-Lab/blob/master/Lab2-ReplicaSets.md)
  #### Important Commands for lab 2:
  - Download [replicaset-definition.yml](https://github.com/juliehub/Kubernetes-Lab/blob/master/replicaset-definition.yml)
  - Run commands:
  ```bash
  $ kubectl create -f replicaset-definition.yml
  $ kubectl get replicatset
  $ kubectl get pods
  $ kubectl get all
  ```
  Scale with 6 replicas
  ```bash
  $ kubectl replace -f replicaset-definition.yml
  $ kubectl scale --replicas=6 -f replicaset-definition.yml
  $ kubectl scale --replicas=6 replicaset myapp-replicaset
  ```
- Lab 3: [Deployments](https://github.com/juliehub/Kubernetes-Lab/blob/master/Lab3-Deployments.md)
  #### Important Commands for lab 3:
  - Download [deployment-definition.yml](https://github.com/juliehub/Kubernetes-Lab/blob/master/deployment-definition.yml)
  - Run commands:
  ```bash
  $ kubectl create -f deployment-definition.yml
  $ kubectl get deployments
  $ kubectl get replicatset
  $ kubectl get pods
  $ kubectl get all
  ```






