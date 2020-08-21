1. Deploy a pod named `nginx-pod` using the `nginx:alpine` image.
Use imperative commands only.
```bash
master $ kubectl run nginx-pod --image=nginx:alpine
pod/nginx-pod created
master $ kubectl get pods
NAME        READY   STATUS              RESTARTS   AGE
nginx-pod   0/1     ContainerCreating   0          38s
```
2. Deploy a `redis` pod using the `redis:alpine` image with the labels set to `tier=db`.
Either use imperative commands to create the pod with the labels.
Or else use imperative commands to generate the pod definition file,
then add the labels before creating the pod using the file.
```bash
master $ kubectl run redis --image=redis:alpine -l tier=db
pod/redis created
master $ kubectl get pods
NAME        READY   STATUS              RESTARTS   AGE
nginx-pod   0/1     ContainerCreating   0          4m35s
redis       0/1     ContainerCreating   0          5s
```
3. Create a service redis-service to expose the redis application within the cluster on port 6379. Use imperative commands
- Service: redis-service
- Port: 6379
- Type: ClusterIp
- Selector: tier=db
```bash
master $ kubectl expose pod redis --port=6379 --name redis-service
service/redis-service exposed
master $ kubectl get svc
NAME            TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
kubernetes      ClusterIP   10.96.0.1     <none>        443/TCP    32m
redis-service   ClusterIP   10.107.52.1   <none>        6379/TCP   22s
```
4. Create a deployment named `webapp` using the image `kodekloud/webapp-color` with `3` replicas.
Try to use imperative commands only. Do not create definition files.
- Name: webapp
- Image: kodekloud/webapp-color
- Replicas: 3
```bash
master $ kubectl create deployment --image=kodekloud/webapp-color webapp --dry-run=client -o yaml > webapp.yaml
master $ vi webapp.yaml
master $ kubectl apply -f webapp.yaml
deployment.apps/webapp created
master $ kubectl get deployments
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
webapp   0/3     3            0           32s
```
Or
Use the command `kubectl create deployment webapp --image=kodekloud/webapp-color`.

Then scale the webapp to 3 using command `kubectl scale deployment/webapp --replicas=3`
5. Create a new pod called `custom-nginx` using the `nginx` image and expose it on container `port 8080`.
```bash
master $ kubectl run custom-nginx --image=nginx --port=8080
pod/custom-nginx created
master $ kubectl get pods
NAME                      READY   STATUS              RESTARTS   AGE
custom-nginx              0/1     ContainerCreating   0          8s
nginx-pod                 0/1     ContainerCreating   0          15m
redis                     0/1     ContainerCreating   0          11m
webapp-7cd5749f95-69cz9   0/1     ContainerCreating   0          2m11s
webapp-7cd5749f95-hnw45   0/1     ContainerCreating   0          2m10s
webapp-7cd5749f95-snznv   0/1     ContainerCreating   0          2m11s
```
6. Create a new namespace called `dev-ns`.
Use imperative commands.
```bash
master $ kubectl create namespace dev-ns
namespace/dev-ns created
```
7. Create a new deployment called `redis-deploy` in the `dev-ns` namespace with the `redis` image. It should have `2` replicas.
Use imperative commands.
- redis-deploy created in the dev-ns namespace?
- replicas: 2
```bash
master $ kubectl create deployment redis-deploy --image redis --namespace=dev-ns --dry-run=client -o yaml > deploy.yaml
```
Edit the YAML file and add update the replicas to 2
```bash
master $ vi deploy.yaml
master $ kubectl apply -f deploy.yaml
deployment.apps/redis-deploy created
master $ kubectl get deployments
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
webapp   0/3     3            0           11m
```
You can also use `kubectl scale deployment` or `kubectl edit deployment` to change the number of replicas once the object has been created.

8. Create a pod called httpd using the image `httpd:alpine` in the default namespace.
Next, create a service of type `ClusterIP` by the same name `(httpd)`. 
The target port for the service should be `80`.
Try to do this with as **few steps as possible**.
- `httpd` pod created with the correct image?
- 'httpd' service is of type 'clusterIP'?
- 'httpd' service uses correct target port 80?
- 'httpd' service exposes the 'httpd' pod?
```bash
master $ kubectl run httpd --image=httpd:alpine --port=80 --expose
service/httpd created
pod/httpd created
master $ kubectl get svc
NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
httpd           ClusterIP   10.99.93.201   <none>        80/TCP     2m19s
kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP    71m
redis-service   ClusterIP   10.107.52.1    <none>        6379/TCP   39m
master $ kubectl get pod
NAME                      READY   STATUS              RESTARTS   AGE
custom-nginx              0/1     ContainerCreating   0          32m
httpd                     0/1     ContainerCreating   0          2m27s
nginx-pod                 0/1     ContainerCreating   0          48m
redis                     0/1     ContainerCreating   0          43m
webapp-7cd5749f95-69cz9   0/1     ContainerCreating   0          34m
webapp-7cd5749f95-hnw45   0/1     ContainerCreating   0          34m
webapp-7cd5749f95-snznv   0/1     ContainerCreating   0          34m
```

