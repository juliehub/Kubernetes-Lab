# Kubernetes-Lab
## Tips
- [kubectl CheatSheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [kubectl Usage Conventions](https://kubernetes.io/docs/reference/kubectl/conventions/)
- [other tips](https://github.com/juliehub/Kubernetes-Lab/blob/master/Tips.md)
## Basics
#### Lab 1: PODs
See details of this lab via [Pods](https://github.com/juliehub/Kubernetes-Lab/blob/master/Basics/Lab1-Pods.md)
##### Important Commands for lab 1:
  - Download [pod-definition.yaml](https://github.com/juliehub/Kubernetes-Lab/blob/master/pod-definition.yaml)
  - Run commands:
  ```bash
  $ kubectl create -f pod-definition.yml
  $ kubectl run nginx --image nginx
  $ kubectl get pods
  $ kubectl describe pod newpods-4rtwk 
  $ kubectl get pods -o wide
  $ kubectl delete pod webapp
  $ kubectl run redis --image=redis123 --dry-run=client -o yaml > pod.yaml
  $ kubectl apply -f pod.yaml
  $ kubectl edit pod redis
  ```
#### Lab 2: ReplicaSet
See details of this lab via [ReplicaSets](https://github.com/juliehub/Kubernetes-Lab/blob/master/Basics/Lab2-ReplicaSets.md)
##### Important Commands for lab 2:
  - Download [replicaset-definition.yaml](https://github.com/juliehub/Kubernetes-Lab/blob/master/replicaset-definition.yaml) and [compute-quota.yaml](https://github.com/juliehub/Kubernetes-Lab/blob/master/compute-quota.yaml)
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
See details of this lab via [Deployments](https://github.com/juliehub/Kubernetes-Lab/blob/master/Basics/Lab3-Deployments.md)
##### Important Commands for lab 3:
  - Download [deployment-definition.yaml](https://github.com/juliehub/Kubernetes-Lab/blob/master/deployment-definition.yaml)
  - Run commands:
  ```bash
  $ kubectl create -f deployment-definition.yml
  $ kubectl get deployments
  $ kubectl get replicatset
  $ kubectl get pods
  $ kubectl get all
  ```
##### Tips for lab 3
- Create an NGINX Pod

`kubectl run --generator=run-pod/v1 nginx --image=nginx`
- Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)

`kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml`
- Create a deployment

`kubectl create deployment --image=nginx nginx`
- Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

`kubectl create deployment --image=nginx nginx --dry-run -o yaml`
- Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)

`kubectl create deployment --image=nginx nginx --dry-run -o yaml > nginx-deployment.yaml`

- Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment.

#### Lab 4: NameSpaces
See details of this lab via [Namespaces](https://github.com/juliehub/Kubernetes-Lab/blob/master/Basics/Lab4-NameSpaces.md)
##### Important Commands for lab 4:
- Download [namespace-dev.yaml](https://github.com/juliehub/Kubernetes-Lab/blob/master/namespace-dev.yaml)
- Run commands:
 ```bash
 $ kubectl get ns
 $ kubectl get pods --namespace=kube-system
 $ kubectl create -f pod-definition.yml --namespace=dev
 $ kubectl create namespace dev
 $ kubectl create -f namespace-dev.yml
```
- Switch namespace
```bash
 $ kubectl config set-context $(kubect config current-context) --namespace=dev
 $ kubectl get pods
 $ kubectl get pods --namespace=default
 $ kubectl get pods --namespace=prod
 $ kubectl get pods --all-namespaces
```
- Create quota
```bash
$ kubectl create -f compute-quota.yml
```
#### Lab 5: Services
See details of this lab via [Services](https://github.com/juliehub/Kubernetes-Lab/blob/master/Basics/Lab5-Services.md)
##### Important Commands for lab 5:
- Download [service-definition.yaml](https://github.com/juliehub/Kubernetes-Lab/blob/master/service-definition.yaml)
- Download [service-clusterIP-definition.yaml](https://github.com/juliehub/Kubernetes-Lab/blob/master/service-clusterIP-definition.yaml)
- Run commands:
```bash
$ kubectl create -f service-definition.yml
$ kubectl get services
$ kubectl get svc
$ kubectl expose deployment simple-webapp-deployment --name=webapp-service --target-port=8080 --type=NodePort --port=8080 --dry-run=client -o yml > svc.yml
$ vi svc.yaml
$ kubectl apply -f svc.yaml
```
- Access the Node port using `curl http://192.168.1.2:30008`
#### Lab 6: Imperative Commands
See details of this lab via [Imperative_Commands](https://github.com/juliehub/Kubernetes-Lab/blob/master/Basics/Lab6-ImperativeCommands.md)
##### Important Commands for lab 6:
- Imperative Commands
```bash
$ kubectl run --image=nginx nginx
$ kubectl create deployment --image=nginx nginx
$ kubectl expose deployment nginx --port 80
$ kubectl edit deployment nginx
$ kubectl scale deployment nginx --replicas=5
$ kubectl set image deployment nginx nginx=nginx:1.18
```
- Create objects
```bash
$ kubectl apply -f nginx.yaml
$ kubectl apply -f /path/to/config-files
```
- Update objects
```bash
$ kubectl apply -f nginx.yaml
$ kubectl apply -f /path/to
```
#### Lab 7: Manual Scheduling
See details of this lab via [Manual Scheduling](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab7-ManualScheduling.md)

#### Lab 8: Labels and Selectors
See details of this lab via [Labels](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab8-Labels.md)
```bash
$kubectl get pods --show-labels
$kubectl get pods -l env=dev --no-headers | wc -l
```
#### Lab 9: Taints and Tolerations
See details of this lab via [Taints And Tolerations](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab9-TaintsAndTolerations.md)
```bash
$ kubectl taint nodes node01 spray=mortein:NoSchedule
$ kubectl describe node node01 |grep Taints
$ kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule-
$ kubectl describe node master|grep Taints
```
Grep 5 lines underneath "tolerations"
```bash
$ kubectl explain pod --recursive | grep -A5 tolerations
```
#### Lab 10: Node Affinity
See details of this lab via [Node Affinity](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab10-NodeAffinity.md)
```bash
$ kubectl get nodes node01 --show-labels
$ kubectl label nodes node01 color=blue
$ kubectl get deployments.apps blue -o yaml > blue.yaml
$ vi blue.yaml
$ kubectl delete deployments.apps blue
$ kubectl apply -f blue.yaml
```
#### Lab 11: Resource Limit
See details of this lab via [Resource Limit](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab11-ResourceLimit.md)
#### Lab 12: Daemon Sets
See details of this lab via [DaemonSets](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab12-DaemonsSets.md)
#### Lab 13: Static Pods
See details of this lab via [StaticPods](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab13-StaticPods.md)
#### Lab 14: Daemon Sets
See details of this lab via [Schedulers](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab14-Schedulers.md)
#### Lab 15: Monitoring
See details of this lab via [Monitoring](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab15-Monitoring.md)
#### Lab 16: Logging
See details of this lab via [Logging](https://github.com/juliehub/Kubernetes-Lab/blob/master/Scheduling-Logging/Lab16-Logging.md)
#### Lab 17: Rolling Updates
See details of this lab via [Rolling Updates](https://github.com/juliehub/Kubernetes-Lab/blob/master/ApplicationLifeCycles/RollingUpdates.md)
#### Lab 18: Commands And Arguments
See details of this lab via [Commands And Arguments](https://github.com/juliehub/Kubernetes-Lab/blob/master/ApplicationLifeCycles/CommandsAndArguments.md)
#### Lab 19: Environment Variables
See details of this lab via [Environment Variables](https://github.com/juliehub/Kubernetes-Lab/blob/master/ApplicationLifeCycles/EnvironmentVariables.md)
#### Lab 20: Secrets
See details of this lab via [Screts](https://github.com/juliehub/Kubernetes-Lab/blob/master/ApplicationLifeCycles/TestSecrets.md)