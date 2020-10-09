1. We have deployed a POD. Inspect the POD and wait for it to start running.
in the current(default) namespace
```bash
controlplane $ kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
webapp   1/1     Running   0          2m4s
```
2. The application stores logs at location /log/app.log. View the logs.
```bash
controlplane $ kubectl exec webapp -- cat /log/app.log
...

[2020-10-09 09:18:16,513] INFO in event-simulator: USER4 logged in
[2020-10-09 09:18:17,514] INFO in event-simulator: USER4 is viewing page3
[2020-10-09 09:18:18,516] INFO in event-simulator: USER2 is viewing page1
[2020-10-09 09:18:19,517] INFO in event-simulator: USER3 is viewing page1
[2020-10-09 09:18:20,518] INFO in event-simulator: USER4 is viewing page1
[2020-10-09 09:18:21,519] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-10-09 09:18:21,520] INFO in event-simulator: USER4 logged in
[2020-10-09 09:18:22,521] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2020-10-09 09:18:22,521] INFO in event-simulator: USER3 is viewing page1
```
3. If the POD was to get deleted now, would you be able to view these logs.
Answer: No, there is no mount for /log directory. There is mount for service account: /var/run/secrets/kubernetes.io/serviceaccount from default-token-x5ftw (ro)
```bash
controlplane $ kubectl describe pod webapp
Name:         webapp
Namespace:    default
Priority:     0
Node:         node01/172.17.0.49
Start Time:   Fri, 09 Oct 2020 09:13:18 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.244.2.2
IPs:
  IP:  10.244.2.2
Containers:
  event-simulator:
    Container ID:   docker://f6915800be3199891ff4602ccd52b77352a04a62cfc1c4e441eaa87ed470260d
    Image:          kodekloud/event-simulator
    Image ID:       docker-pullable://kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 09 Oct 2020 09:13:25 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      LOG_HANDLERS:  file
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-x5ftw (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-x5ftw:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-x5ftw
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  7m31s  default-scheduler  Successfully assigned default/webapp to node01
  Normal  Pulling    7m29s  kubelet, node01    Pulling image "kodekloud/event-simulator"
  Normal  Pulled     7m25s  kubelet, node01    Successfully pulled image "kodekloud/event-simulator" in 4.831385691s
  Normal  Created    7m24s  kubelet, node01    Created container event-simulator
  Normal  Started    7m24s  kubelet, node01    Started container event-simulator
```
4. Configure a volume to store these logs at /var/log/webapp on the host
Use the spec given on the right.
- Name: webapp
- Image Name: kodekloud/event-simulator
- Volume HostPath: /var/log/webapp
- Volume Mount: /log
```bash
controlplane $ kubectl get pod webapp -o yaml > pod.yaml
controlplane $ vi pod.yaml
controlplane $ cat pod.yaml
...
spec:
  containers:
  - env:
    - name: LOG_HANDLERS
      value: file
    image: kodekloud/event-simulator
    name: event-simulator
    volumeMounts:
    - mountPath: /log
      name: log-volume
  volumes:
  - name: log-volume
    hostPath:
      # directory location on host
      path: /var/log/webapp
      # this field is optional
      type: Directory
controlplane $ kubectl delete pod webapp
pod "webapp" deleted
controlplane $ kubectl create -f pod.yaml
pod/webapp created
```
5. Create a 'Persistent Volume' with the given specification.
- Volume Name: pv-log
- Storage: 100Mi
- Access modes: ReadWriteMany
- Host Path: /pv/log
```bash
controlplane $ vi pv.yaml
controlplane $ cat pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /pv/log
controlplane $ kubectl explain persistentvolume --recursive | less
controlplane $ kubectl create -f pv.yaml
persistentvolume/pv-log created
controlplane $ kubectl get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-log   100Mi      RWX            Retain           Available                                   3s
controlplane $ kubectl describe pv pv-log
Name:            pv-log
Labels:          <none>
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:
Status:          Available
Claim:
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        100Mi
Node Affinity:   <none>
Message:
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /pv/log
    HostPathType:
Events:            <none>
```
6. Let us claim some of that storage for our application. Create a 'Persistent Volume Claim' with the given specification.
- Volume Name: claim-log-1
- Storage Request: 50Mi
- Access modes: ReadWriteOnce
```bash
controlplane $ cat  /var/answers/claim-log-1.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Micontrolplane
controlplane $ kubectl create -f /var/answers/claim-log-1.yaml
persistentvolumeclaim/claim-log-1 created
```
7. What is the state of the Persistent Volume Claim? `Pending`
(There is a mismatch of accessModes between pv and pvc)
```bash
controlplane $ kubectl get pvc
NAME          STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Pending                                                     59s
```
8. What is the state of the Persistent Volume? `Available`
```bash
controlplane $ kubectl get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-log   100Mi      RWX            Retain           Available                                   4m33s
```
9. Why is the Claim not bound to the available Persistent Volume?
Answer: AccessModes mismatch

10. Update the Access Mode on the claim to bind it to the PVC.
Delete and recreate the claim-log-1
- Volume Name: claim-log-1
- Storage Request: 50Mi
- PVol: pv-log
- Status: Bound
```bash
controlplane $ kubectl delete pvc claim-log-1
persistentvolumeclaim "claim-log-1" deleted
controlplane $ cat /var/answers/claim-log-1-new.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 50Micontrolplane 
controlplane $ kubectl create -f /var/answers/claim-log-1-new.yaml
persistentvolumeclaim/claim-log-1 created
controlplane $ kubectl get pvc
NAME          STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Bound    pv-log   100Mi      RWX                           8s
controlplane $ kubectl get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   REASON   AGE
pv-log   100Mi      RWX            Retain           Bound    default/claim-log-1                           7m12s
```
11. You requested for 50Mi, how much capacity is now available to the PVC? 100Mi
```bash
NAME          STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Bound    pv-log   100Mi      RWX                           70s
```
12. Update the webapp pod to use the persistent volume claim as its storage.
Replace hostPath configured earlier with the newly created PersistentVolumeClaim
- Name: webapp
- Image Name: kodekloud/event-simulator
- Volume: PersistentVolumeClaim=claim-log-1
- Volume Mount: /log
```bash
controlplane $ vi pod.yaml
controlplane $ cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
...
...
spec:
  containers:
  - env:
    - name: LOG_HANDLERS
      value: file
    image: kodekloud/event-simulator
    name: event-simulator
    volumeMounts:
    - mountPath: /log
      name: log-volume
  volumes:
  - name: log-volume
    persistentVolumeClaim:
        claimName: claim-log-1
controlplane $ kubectl delete pod webapp
pod "webapp" deleted
controlplane $ kubectl create -f pod.yaml
pod/webapp created
controlplane $ kubectl describe pod webapp
Name:         webapp
Namespace:    default
Priority:     0
Node:         node01/172.17.0.49
Start Time:   Fri, 09 Oct 2020 10:09:38 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.244.2.4
IPs:
  IP:  10.244.2.4
Containers:
  event-simulator:
    Container ID:   docker://4e55bc2c79fc6b7e09b4ee0a6300f590d7627f707053af0eb89643ec2d4ee71c
    Image:          kodekloud/event-simulator
    Image ID:       docker-pullable://kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 09 Oct 2020 10:09:41 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      LOG_HANDLERS:  file
    Mounts:
      /log from log-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-x5ftw (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  log-volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  claim-log-1
    ReadOnly:   false
  default-token-x5ftw:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-x5ftw
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  16s   default-scheduler  Successfully assigned default/webapp to node01
  Normal  Pulling    15s   kubelet, node01    Pulling image "kodekloud/event-simulator"
  Normal  Pulled     14s   kubelet, node01    Successfully pulled image "kodekloud/event-simulator" in 1.332072818s
  Normal  Created    13s   kubelet, node01    Created container event-simulator
  Normal  Started    13s   kubelet, node01    Started container event-simulator
```
13. What is the Reclaim Policy set on the Persistent Volume - 'pv-log'
Answer: Retain
```bash
controlplane $ kubectl describe pv pv-log
Name:            pv-log
Labels:          <none>
Annotations:     pv.kubernetes.io/bound-by-controller: yes
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:
Status:          Bound
Claim:           default/claim-log-1
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        100Mi
Node Affinity:   <none>
Message:
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /pv/log
    HostPathType:
Events:            <none>
```
14. What would happen to the PV if the PVC was destroyed?
Answer: The PV is not deleted but not available

15. Try deleting the PVC and notice what happens.
Answer: The PVC is stuck in `terminating` state.
```bash
controlplane $ kubectl delete pvc claim-log-1
persistentvolumeclaim "claim-log-1" deleted
^C
controlplane $ kubectl get pvc claim-log-1
NAME          STATUS        VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Terminating   pv-log   100Mi      RWX                           9m36s
```
16. Why is the PVC stuck in 'Terminating' state?
Answer: the PVC is being used by the pod

17. Let us now delete the 'webapp' Pod.
Once deleted, wait for the pod to fully terminate.
```bash
controlplane $ kubectl  delete pod webapp
pod "webapp" deleted
controlplane $ kubectl get pod,pvc
No resources found in default namespace.
controlplane $ kubectl get pvc,pv
NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                 STORAGECLASS   REASON AGE
persistentvolume/pv-log   100Mi      RWX            Retain           Released   default/claim-log-1 20m
```
18. What is the state of the PVC now?
Answer: Deleted

19. What is the state of the Persistent Volume now?
Answer: Released

