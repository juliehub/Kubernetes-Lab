1. Get number of pods for environment `dev`
```bash
master $ kubectl get pods --selector env=dev
NAME          READY   STATUS    RESTARTS   AGE
app-1-45ntx   1/1     Running   0          2m19s
app-1-s65sf   1/1     Running   0          2m19s
app-1-wdw7f   1/1     Running   0          2m19s
db-1-5wj57    1/1     Running   0          2m19s
db-1-9xb8j    1/1     Running   0          2m19s
db-1-dxsnl    1/1     Running   0          2m19s
db-1-xgskp    1/1     Running   0          2m19s
```
2. Show numer of PODs are in the `finance` business unit ('bu')?
```bash
master $ kubectl get pods --selector bu=finance
NAME          READY   STATUS    RESTARTS   AGE
app-1-45ntx   1/1     Running   0          4m11s
app-1-s65sf   1/1     Running   0          4m11s
app-1-wdw7f   1/1     Running   0          4m11s
app-1-zzxdf   1/1     Running   0          4m10s
auth          1/1     Running   0          4m10s
db-2-lsm46    1/1     Running   0          4m10s
```
3. How many objects are in the `prod` environment including PODs, ReplicaSets and any other objects?
```bash
master $ kubectl get all --selector env=prod
NAME              READY   STATUS    RESTARTS   AGE
pod/app-1-zzxdf   1/1     Running   0          6m15s
pod/app-2-8lp2w   1/1     Running   0          6m16s
pod/auth          1/1     Running   0          6m15s
pod/db-2-lsm46    1/1     Running   0          6m15s

NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/app-1   ClusterIP   10.106.113.251   <none>        3306/TCP   6m15s

NAME                    DESIRED   CURRENT   READY   AGE
replicaset.apps/app-2   1         1         1       6m16s
replicaset.apps/db-2    1         1         1       6m15s
```
4. Identify the POD which is 'prod', part of 'finance' BU and is a 'frontend' tier?
```bash
master $ kubectl get all --selector env=prod,bu=finance,tier=frontend
NAME              READY   STATUS    RESTARTS   AGE
pod/app-1-zzxdf   1/1     Running   0          13m
```
