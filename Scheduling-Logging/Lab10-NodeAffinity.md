1. Display number of labels for node01
```bash
master $ kubectl describe node node01 |grep -A5 Labels
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=node01
                    kubernetes.io/os=linux
Annotations:        flannel.alpha.coreos.com/backend-data: {"VtepMAC":"12:c5:79:5d:c8:cb"}
```
2. What is the value set to the label beta.kubernetes.io/arch on node01?
`amd64`
3. Apply a label color=blue to node node01
```bash
master $ kubectl label node node01 color=blue
node/node01 labeled
master $ kubectl describe node node01 |grep -A5 Labels
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    color=blue
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=node01
                    kubernetes.io/os=linux
```
4. Create a new deployment named 'blue' with the NGINX image and 6 replicas
```bash
master $ kubectl create deployment --image=nginx blue
deployment.apps/blue created
master $ kubectl scale deployment blue --replicas=6
deployment.apps/blue scaled
master $ kubectl get deployments
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
blue   0/6     6            0           11s
```
5. Which nodes can the PODs placed on? both master and node01
```bash
master $ kubectl describe node node01 |grep Taints
Taints:             <none>
master $ kubectl describe node master |grep Taints
Taints:             <none>
```
6. Set Node Affinity to the deployment to place the PODs on node01 only
```bash
master $ kubectl apply -f /var/answers/blue-deployment.yaml
deployment.apps/blue created
```
7. Which nodes are the PODs placed on now?
```bash
master $ kubectl get pods -o wide
NAME                   READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
blue-98df9cf77-4sllf   1/1     Running   0          38s   10.244.1.19   node01   <none>           <none>
blue-98df9cf77-9lllj   1/1     Running   0          38s   10.244.1.17   node01   <none>           <none>
blue-98df9cf77-gdcw5   1/1     Running   0          38s   10.244.1.20   node01   <none>           <none>
blue-98df9cf77-h9kph   1/1     Running   0          38s   10.244.1.18   node01   <none>           <none>
blue-98df9cf77-qmnzf   1/1     Running   0          38s   10.244.1.15   node01   <none>           <none>
blue-98df9cf77-qr7vk   1/1     Running   0          38s   10.244.1.16   node01   <none>           <none>
```
8. Create a new deployment named `red` with the NGINX image and 3 replicas, and ensure it gets placed on the master node only.
Use the label - `node-role.kubernetes.io/master` - set on the master node.
```bash
master $ cat /var/answers/red-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: red
spec:
  replicas: 3
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
master $ kubectl apply -f /var/answers/red-deployment.yaml
deployment.apps/red created
```
