1. We have an application running on our cluster. Let us explore it first. What image is the application using? `nginx:alpine`
```bash
master $ kubectl get deployments
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
web    2/2     2            2           3m32s
master $ kubectl describe deployment web
Name:                   web
Namespace:              default
CreationTimestamp:      Wed, 30 Sep 2020 04:53:35 +0000
Labels:                 app=web
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=web
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=web
  Containers:
   nginx:
    Image:        nginx:alpine
```
2. We decided to use a modified version of the application from an internal private registry. Update the image of the deployment to use a new image from myprivateregistry.com:5000
The registry is located at myprivateregistry.com:5000. Don't worry about the credentials for now. We will configure them in the upcoming steps.

Edit the image name to myprivateregistry.com:5000/nginx:alpine
```bash
master $ kubectl edit deployment
deployment.apps/web edited
```
3. Are the new PODs created with the new images successfully running? No
```bash
master $ kubectl get pods
NAME                   READY   STATUS             RESTARTS   AGE
web-65bc677cf5-5kzzf   0/1     ImagePullBackOff   0          43s
web-757b88fd8d-5r7qv   1/1     Running            0          10m
web-757b88fd8d-l46jk   1/1     Running            0          10m
```
```bash
master $ kubectl describe pod web-65bc677cf5-5kzzf
Name:         web-65bc677cf5-5kzzf
Namespace:    default
Priority:     0
Node:         node01/172.17.0.10
Start Time:   Wed, 30 Sep 2020 05:03:56 +0000
Labels:       app=web
              pod-template-hash=65bc677cf5
Annotations:  <none>
Status:       Pending
IP:           10.244.1.5
IPs:
  IP:           10.244.1.5
Controlled By:  ReplicaSet/web-65bc677cf5
Containers:
  nginx:
    Container ID:
    Image:          myprivateregistry.com:5000/nginx:alpine
    Image ID:
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-whzpw (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-whzpw:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-whzpw
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  68s                default-scheduler  Successfully assigned default/web-65bc677cf5-5kzzf to node01
  Normal   Pulling    33s (x3 over 68s)  kubelet, node01    Pulling image "myprivateregistry.com:5000/nginx:alpine"
  Warning  Failed     33s (x3 over 67s)  kubelet, node01    Failed to pull image "myprivateregistry.com:5000/nginx:alpine": rpc error: code = Unknown desc = Error response from daemon: Get http://myprivateregistry.com:5000/v2/: net/http: HTTP/1.x transport connection broken: malformed HTTP response "\x15\x03\x01\x00\x02\x02"
  Warning  Failed     33s (x3 over 67s)  kubelet, node01    Error: ErrImagePull
  Normal   BackOff    4s (x5 over 67s)   kubelet, node01    Back-off pulling image "myprivateregistry.com:5000/nginx:alpine"
  Warning  Failed     4s (x5 over 67s)   kubelet, node01    Error: ImagePullBackOff
```
4. Create a secret object with the credentials required to access the registry
- Name: private-reg-cred
-  Username: dock_user
- Password: dock_password
- Server: myprivateregistry.com:5000
- Email: dock_user@myprivateregistry.com
```bash
master $ kubectl create secret docker-registry private-reg-cred --docker-username=dock_user --docker-password=dock_password --docker-server=myprivateregistry.com:5000 --docker-email=dock_user@myprivateregistry.com
secret/private-reg-cred created
```
5. Configure the deployment to use credentials from the new secret to pull images from the private registry
```bash
master $ kubectl edit deploy web
deployment.apps/web edited
```
```bash
master $ kubectl get deployment web -o yaml > web.yaml
master $ cat web.yaml
...
    spec:
      containers:
      - image: myprivateregistry.com:5000/nginx:alpine
        imagePullPolicy: IfNotPresent
        name: nginx
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: private-reg-cred
```
6. Check the status of PODs. Wait for them to be running. You have now successfully configured a Deployment to pull images from the private registry
```bash
master $ kubectl get pods
NAME                  READY   STATUS    RESTARTS   AGE
web-94b85bcfd-jgcsd   1/1     Running   0          2m8s
web-94b85bcfd-p4dz6   1/1     Running   0          2m10s
```