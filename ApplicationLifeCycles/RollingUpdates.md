1. Run the script named curl-test.sh to send multiple requests to test the web application. Take a note of the output.
Execute the script at `/root/curl-test.sh`.
```bash
master $ cat curl-test.sh
for i in {1..35}; do
   kubectl exec --namespace=kube-public curl -- sh -c 'test=`wget -qO- -T 2  http://webapp-service.default.svc.cluster.local:8080/info 2>&1` && echo "$test OK" || echo "Failed"';
   echo ""
done
master $ ./curl-test.sh
Hello, Application Version: v1 ; Color: blue OK

Hello, Application Version: v1 ; Color: blue OK

Hello, Application Version: v1 ; Color: blue OK
```
2. Inspect the deployment and identify the number of PODs deployed by it. Answer: 4
```bash
master $ kubectl describe deployment
Name:                   frontend
Namespace:              default
CreationTimestamp:      Sun, 20 Sep 2020 11:09:31 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               name=webapp
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        20
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  name=webapp
  Containers:
   simple-webapp:
    Image:        kodekloud/webapp-color:v1
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
NewReplicaSet:   frontend-6bb4f9cdc8 (4/4 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  6m44s  deployment-controller  Scaled up replica set frontend-6bb4f9cdc8 to 4
  ```
3. What container image is used to deploy the applications? `kodekloud/webapp-color:v1`

4. Inspect the deployment and identify the current strategy. Answer: RollingUpdate

5. If you were to upgrade the application now what would happen?
PODS are upgraded few at a time

6. Let us try that. Upgrade the application by setting the image on the deployment to 'kodekloud/webapp-color:v2'
Do not delete and re-create the deployment. Only set the new image name for the existing deployment.
- Deployment Name: frontend
- Deployment Image: kodekloud/webapp-color:v2
```bash
master $ kubectl set image deployment/frontend simple-webapp=kodekloud/webapp-color:v2
deployment.apps/frontend image updated
master $ kubectl describe deployment
Name:                   frontend
Namespace:              default
CreationTimestamp:      Sun, 20 Sep 2020 11:09:31 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               name=webapp
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        20
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  name=webapp
  Containers:
   simple-webapp:
    Image:        kodekloud/webapp-color:v2
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
NewReplicaSet:   frontend-b84595c9 (4/4 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  22m   deployment-controller  Scaled up replica set frontend-6bb4f9cdc8 to 4
  Normal  ScalingReplicaSet  77s   deployment-controller  Scaled up replica set frontend-b84595c9 to 1
  Normal  ScalingReplicaSet  77s   deployment-controller  Scaled down replica set frontend-6bb4f9cdc8 to 3
  Normal  ScalingReplicaSet  77s   deployment-controller  Scaled up replica set frontend-b84595c9 to 2
  Normal  ScalingReplicaSet  52s   deployment-controller  Scaled down replica set frontend-6bb4f9cdc8 to 1
  Normal  ScalingReplicaSet  52s   deployment-controller  Scaled up replica set frontend-b84595c9 to 4
  Normal  ScalingReplicaSet  29s   deployment-controller  Scaled down replica set frontend-6bb4f9cdc8 to 0
```
7. Change the strategy type to `Recreate`
```bash
master $ kubectl edit deployment frontend
deployment.apps/frontend edited
```
8. Upgrade the application by setting the image on the deployment to 'kodekloud/webapp-color:v3'
Do not delete and re-create the deployment. Only set the new image name for the existing deployment.
- Deployment Name: frontend
- Deployment Image: kodekloud/webapp-color:v3

```bash
master $ kubectl edit deployment frontend
deployment.apps/frontend edited
...
