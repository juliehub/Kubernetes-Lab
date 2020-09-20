1. How many services?

Answer: 1
```bash
master $ kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   5m21s
```
2. What is the 'targetPort' configured on the 'kubernetes' service?

Answer: 6443
```bash
master $ kubectl describe service kubernetes
Name:              kubernetes
Namespace:         default
Labels:            component=apiserver
                   provider=kubernetes
Annotations:       <none>
Selector:          <none>
Type:              ClusterIP
IP:                10.96.0.1
Port:              https  443/TCP
TargetPort:        6443/TCP
Endpoints:         172.17.0.72:6443
Session Affinity:  None
Events:            <none>
```
3. How many labels are configured on the 'kubernetes' service?

Answer: 2

4. How many Endpoints are attached on the 'kubernetes' service?

Answer: 1

5. How many Deployments exist on the system now?
in the current(default) namespace

Answer: 1
```bash
master $ kubectl get deployment
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
simple-webapp-deployment   4/4     4            4           39s
```

6. What is the image used to create the pods in the deployment?

Answer: kodekloud/simple-webapp:red
```bash
master $ kubectl describe deployment simple-webapp-deployment
Name:                   simple-webapp-deployment
Namespace:              default
CreationTimestamp:      Thu, 20 Aug 2020 11:31:07 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               name=simple-webapp
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  name=simple-webapp
  Containers:
   simple-webapp:
    Image:        kodekloud/simple-webapp:red
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   simple-webapp-deployment-677668f6b (4/4 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m10s  deployment-controller  Scaled up replica set simple-webapp-deployment-677668f6b to 4
 ```
 7. Are you able to accesss the Web App UI?
Try to access the Web Application UI using the tab simple-webapp-ui above the terminal.

Answer: Yes

8. Create a new service to access the web application using the service-definition-1.yaml file

Name: webapp-service; Type: NodePort; targetPort: 8080; port: 8080; nodePort: 30080; selector: simple-webapp
```bash
master $ kubectl expose deployment simple-webapp-deployment --name=webapp-service --target-port=8080 --type=NodePort --port=8080 --dry-run=client -o yml > svc.yml
master $ vi svc.yaml
master $ kubectl apply -f svc.yaml
service/webapp-service created
```
