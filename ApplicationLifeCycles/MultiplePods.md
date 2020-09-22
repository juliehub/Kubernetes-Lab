1. Identify the number of containers running in the 'red' pod. 3
```bash
master $ kubectl get pods
NAME   READY   STATUS             RESTARTS   AGE
red    0/3     CrashLoopBackOff   3          22s
```
2. Create a multi-container pod with 2 containers.
Use the spec given on the right.
- Name: yellow
- Container 1 Name: lemon
- Container 1 Image: busybox
- Container 2 Name: gold
- Container 2 Image: redis
```bash
master $ kubectl run yellow --image=busybox --restart=Never --dry-run=client -o yaml > pod.yaml
master $ vi pod.yaml
master $ cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: yellow
  name: yellow
spec:
  containers:
  - image: busybox
    name: lemon
  - image: redis
    name: gold
master $ kubectl apply -f pod.yaml
pod/yellow created
```
3. We have deployed an application logging stack in the elastic-stack namespace. Inspect it.
```bash
master $ kubectl get ns
NAME              STATUS   AGE
default           Active   22m
elastic-stack     Active   21m
kube-node-lease   Active   22m
kube-public       Active   22m
kube-system       Active   22m
master $ kubectl -n elastic-stack get pod,svc
NAME                 READY   STATUS    RESTARTS   AGE
pod/app              1/1     Running   0          22m
pod/elastic-search   1/1     Running   0          22m
pod/kibana           1/1     Running   0          22m

NAME                    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)                         AGE
service/elasticsearch   NodePort   10.96.221.73    <none>        9200:30200/TCP,9300:30300/TCP   22m
service/kibana          NodePort   10.101.96.194   <none>        5601:30601/TCP                  22m
```
4. Inspect the 'app' pod and identify the number of containers in it. Answer: 1
It is deployed in the elastic-stack namespace

5. The 'app'lication outputs logs to the file /log/app.log. View the logs and try to identify the user having issues with Login.
Inspect the log file inside the pod
```bash
master $ kubectl -n elastic-stack logs app | grep WARNING
[2020-09-22 00:03:13,185] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-09-22 00:03:15,193] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
```
or 
```bash
kubectl -n elastic-stack exec -it app cat /log/app.log
```
6. Edit the pod to add a sidecar container to send logs to ElasticSearch. Mount the log volume to the sidecar container..
Only add a new container. Do not modify anything else. Use the spec on the right.
- Name: app
- Container Name: sidecar
- Container Image: kodekloud/filebeat-configured
- Volume Mount: log-volume
- Mount Path: /var/log/event-simulator/
- Existing Container Name: app
- Existing Container Image: kodekloud/event-simulator
```bash
master $ kubectl -n elastic-stack get pod -o yaml > app.yaml
master $ kubectl -n elastic-stack delete pod app
pod "app" deleted
master $ vi app.yaml
```
Add the below
```bash
master $ more app.yaml
apiVersion: v1
items:
- apiVersion: v1
  kind: Pod
  metadata:
    labels:
      name: app
    name: app
    namespace: elastic-stack
  spec:
    containers:
    - image: kodekloud/event-simulator
      imagePullPolicy: Always
      name: app
      volumeMounts:
      - mountPath: /log
        name: log-volume
    - image: kodekloud/filebeat-configured
      name: sidecar
      volumeMounts:
      - mountPath: /var/log/event-simulator/
        name: log-volume
    nodeName: node01
master $ kubectl apply -f app.yaml
pod/app created
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
pod/elastic-search configured
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
pod/kibana configured
```
7. Inspect the Kibana UI. You should now see logs appearing in the 'Discover' section.
You might have to wait for a couple of minutes for the logs to populate. You might have to create an index pattern to list the logs. If not sure check this video: https://bit.ly/2EXYdHf
https://www.loom.com/share/c2ae70197e8340a0ba77fc1de8179182
