1. A pod named 'rabbit' is deployed. Identify the CPU requirements set on the Pod in the current(default) namespace.

Answer: 1
```bash
master $ kubectl describe pod rabbit | grep -A5 Limits
    Limits:
      cpu:  2
    Requests:
      cpu:        1
    Environment:  <none>
    Mounts:
```
2. Delete the 'rabbit' Pod.
```bash
master $ kubectl delete pod rabbit
pod "rabbit" deleted
```
3. Inspect the pod elephant and identify the status.
```bash
master $ kubectl get pods elephant
NAME       READY   STATUS      RESTARTS   AGE
elephant   0/1     OOMKilled   2          28s
```
4. The status 'OOMKilled' indicates that the pod ran out of memory. Identify the memory limit set on the POD.
5. The elephant runs a process that consume 15Mi of memory. Increase the limit of the elephant pod to 20Mi.
Delete and recreate the pod if required. Do not modify anything other than the required fields.
```bash
master $ kubectl get pods elephant -o yaml > elephant.yaml
master $ vi elephant.yaml
master $ kubectl create -f elephant.yaml
pod/elephant created
master $ kubectl get pod elephant
NAME       READY   STATUS    RESTARTS   AGE
elephant   1/1     Running   0          17s
master $ kubectl describe pod elephant |grep -A10  Limits
    Limits:
      memory:  20Mi
    Requests:
      memory:     5Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-nv47q (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
```
