1. How many StorageClasses exist in the cluster right now? `0`
```bash
master $ kubectl get sc
No resources found in default namespace.
```
2. How about now? How many Storage Classes exist in the cluster? `2`
We just created a few new Storage Classes. Inspect them.
```bash
master $ kubectl get sc
NAME                        PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-storage               kubernetes.io/no-provisioner    Delete          WaitForFirstConsumer   false                  11s
portworx-io-priority-high   kubernetes.io/portworx-volume   Delete          Immediate              false                  11s
```
3. What is the name of the `Storage Class` that does not support `dynamic` volume provisioning? `local-storage` (Look for the storage class name that uses no-provisioner)

4. What is the `Volume Binding Mode` used for this storage class (the one identified in the previous question).
Answer: `WaitForFirstConsumer`
```bash
master $ kubectl describe sc local-storageName:            local-storage
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{},"name":"local-storage"},"provisioner":"kubernetes.io/no-provisioner","volumeBindingMode":"WaitForFirstConsumer"}

Provisioner:           kubernetes.io/no-provisioner
Parameters:            <none>
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     WaitForFirstConsumer
Events:                <none>
```
5. What is the `Provisioner` used for the storage class called portworx-io-priority-high? `kubernetes.io/portworx-volume`

6. Is there a `PersistentVolumeClaim` that is consuming the `PersistentVolume` called local-pv? No
```bash
master $ kubectl get pvc
No resources found in default namespace.
```

7. Let's fix that. Create a new `PersistentVolumeClaim` by the name of `local-pvc` that should bind to the volume `local-pv`
Inspect the pv `local-pv` for the specs.
```bash
master $ cat pvc.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: local-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: local-storage
  resources:
    requests:
      storage: 500Mi
master $ kubectl create -f /var/answers/pvc.yaml
persistentvolumeclaim/local-pvc created
master $ kubectl get pvc
NAME        STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS    AGE
local-pvc   Pending                                      local-storage   6s
```
8. What is the status of the newly created Persistent Volume Claim? `Pending`

9. Why is the PVC in a pending state despite making a valid request to claim the volume called local-pv?
Inspect the PVC events.
Answer: waiting for first consumer to be created before binding. A pod consuming the volume is not scheduled.
```bash
master $ kubectl describe pvc local-pvc
Name:          local-pvc
Namespace:     default
StorageClass:  local-storage
Status:        Pending
Volume:
Labels:        <none>
Annotations:   <none>
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:
Access Modes:
VolumeMode:    Filesystem
Mounted By:    <none>
Events:
  Type    Reason                Age              From                         Message
  ----    ------                ----             ----                         -------
  Normal  WaitForFirstConsumer  6s (x9 over 2m)  persistentvolume-controller  waiting for first consumer to be created before binding
```

10. The Storage Class called `local-storage` makes use of `VolumeBindingMode` set to `WaitForFirstConsumer`. This will delay the binding and provisioning of a PersistentVolume until a Pod using the PersistentVolumeClaim is created.

Create a new pod called `nginx` with the image `nginx:alpine`. The Pod should make use of the PVC `local-pvc` and mount the volume at the path `/var/www/html`.
The PV local-pv should in a bound state.
```bash
master $ cat /var/answers/nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    volumeMounts:
      - name: local-persistent-storage
        mountPath: /var/www/html
  volumes:
    - name: local-persistent-storage
      persistentVolumeClaim:
        claimName: local-pvc
master $ kubectl create -f  /var/answers/nginx.yaml
pod/nginx created
master $ kubectl get pvc
NAME        STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
local-pvc   Bound    local-pv   500Mi      RWO            local-storage   6m50s
```
11. Create a new Storage Class called `delayed-volume-sc` that makes use of the below specs:
- provisioner: kubernetes.io/no-provisioner
- volumeBindingMode: WaitForFirstConsumer
```bash
master $ cat /var/answers/delay-volume-sc.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: delayed-volume-sc
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
master $ kubectl create -f /var/answers/delay-volume-sc.yaml
storageclass.storage.k8s.io/delayed-volume-sc created
master $ kubectl get sc
NAME                        PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
delayed-volume-sc           kubernetes.io/no-provisioner    Delete          WaitForFirstConsumer   false                  119s
local-storage               kubernetes.io/no-provisioner    Delete          WaitForFirstConsumer   false                  24m
portworx-io-priority-high   kubernetes.io/portworx-volume   Delete          Immediate              false                  24m
master $
```




