1. How many Nodes exist on the system (including the master node)?
```bash
master $ kubectl get nodes
NAME     STATUS   ROLES    AGE   VERSION
master   Ready    master   97s   v1.18.0
node01   Ready    <none>   69s   v1.18.0
```
2. Do any taints exist on `node01`? No
```bash
master $ kubectl describe node node01 |grep Taints
Taints:             <none>
```
3. Create a taint on node01 with key of 'spray', value of 'mortein' and effect of 'NoSchedule'
```bash
master $ kubectl taint nodes node01 spray=mortein:NoSchedule
node/node01 tainted
master $ kubectl describe node node01 |grep Taints
Taints:             spray=mortein:NoSchedule
```
4. Create a new pod with the NGINX image, and Pod name as 'mosquito'
```bash
master $ cat /var/answers/mosquito.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mosquito
spec:
  containers:
  - image: nginx
    name: mosquito
master $ kubectl apply -f mosquito.yaml
pod/mosquito created
master $ kubectl get pods
NAME       READY   STATUS    RESTARTS   AGE
mosquito   0/1     Pending   0          8s
```

or
```bash
$ kubectl run mosquito --image=nginx --restart=Never
```
5. Why do you think the pod is in a pending state?
POD `mosquito` cannot tolerate taint Mortein

6. Create another pod named 'bee' with the NGINX image, which has a toleration set to the taint Mortein
```bash
master $ cat /var/answers/bee.yaml
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
  tolerations:
  - key: spray
    value: mortein
    effect: NoSchedule
    operator: Equal
master $ kubectl apply -f /var/answers/bee.yaml
pod/bee created
master $ kubectl get pods
NAME       READY   STATUS    RESTARTS   AGE
bee        1/1     Running   0          49s
mosquito   0/1     Pending   0          7m57s
master $ kubectl describe pod bee|grep node
Node:         node01/172.17.0.61
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
  Normal  Scheduled  2m43s  default-scheduler  Successfully assigned default/bee to node01
  Normal  Pulling    2m42s  kubelet, node01    Pulling image "nginx"
  Normal  Pulled     2m36s  kubelet, node01    Successfully pulled image "nginx"
  Normal  Created    2m35s  kubelet, node01    Created container bee
  Normal  Started    2m35s  kubelet, node01    Started container bee
```
or
```bash
$ kubectl run bee --image=nginx --restart=Never --dry-run -o yaml > bee.yaml
$ kubectl explain pod --recursive | grep -A5 tolerations
```
7. Do you see any taints on master node?
```bash
master $ kubectl describe node master|grep Taints
Taints:             node-role.kubernetes.io/master:NoSchedule
```
8. Remove the taint on master, which currently has the taint effect of `NoSchedule`
```bash
master $ kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule-
node/master untainted
master $ kubectl describe node master|grep Taints
Taints:             <none>
```
9. What is the state of the pod 'mosquito' now? `Running`
```bash
master $ kubectl get pods
NAME       READY   STATUS    RESTARTS   AGE
bee        1/1     Running   0          5m57s
mosquito   1/1     Running   0          13m
```
10. Which node is the POD 'mosquito' on now? `master`
```bash
master $ kubectl describe pod mosquito|grep Node
Node:         master/172.17.0.60
Node-Selectors:  <none>
```
