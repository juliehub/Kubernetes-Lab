1. How many PODs exist on the system? `
in the current(default) namespace
```bash
master $ kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
webapp-color   1/1     Running   0          58s
```
2. What is the environment variable name set on the container in the pod? APP_COLOR
```bash
master $ kubectl describe pod webapp-color
Name:         webapp-color
Namespace:    default
Priority:     0
Node:         node01/172.17.0.22
Start Time:   Mon, 21 Sep 2020 05:50:10 +0000
Labels:       name=webapp-color
Annotations:  <none>
Status:       Running
IP:           10.244.1.3
IPs:
  IP:  10.244.1.3
Containers:
  webapp-color:
    Container ID:   docker://538d05da60e088a5622b646614e88e8c568adce29273b02d5ed2bc11da013c0e
    Image:          kodekloud/webapp-color
    Image ID:       docker-pullable://kodekloud/webapp-color@sha256:99c3821ea49b89c7a22d3eebab5c2e1ec651452e7675af243485034a72eb1423
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 21 Sep 2020 05:50:19 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      APP_COLOR:  pink
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-s5dvl (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-s5dvl:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-s5dvl
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  117s  default-scheduler  Successfully assigned default/webapp-color to node01
  Normal  Pulling    115s  kubelet, node01    Pulling image "kodekloud/webapp-color"
  Normal  Pulled     108s  kubelet, node01    Successfully pulled image "kodekloud/webapp-color"
  Normal  Created    108s  kubelet, node01    Created container webapp-color
  Normal  Started    108s  kubelet, node01    Started container webapp-color
```
3. What is the value set on the environment variable APP_COLOR on the container in the pod? pink

4. Update the environment variable on the POD to display a 'green' background
Note: Delete and recreate the POD. Only make the necessary changes. Do not modify the name of the Pod.
Pod Name: webapp-color
Label Name: webapp-color
Env: APP_COLOR=green
```bash
master $ kubectl get pod webapp-color -o yaml > pod.yaml
master $ vi pod.yaml
master $ cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2020-09-21T06:05:38Z"
  labels:
    name: webapp-color
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:labels:
          .: {}
          f:name: {}
      f:spec:
        f:containers:
          k:{"name":"webapp-color"}:
            .: {}
            f:env:
              .: {}
              k:{"name":"APP_COLOR"}:
                .: {}
                f:name: {}
                f:value: {}
            f:image: {}
            f:imagePullPolicy: {}
            f:name: {}
            f:resources: {}
            f:terminationMessagePath: {}
            f:terminationMessagePolicy: {}
        f:dnsPolicy: {}
        f:enableServiceLinks: {}
        f:restartPolicy: {}
        f:schedulerName: {}
        f:securityContext: {}
        f:terminationGracePeriodSeconds: {}
    manager: python-requests
    operation: Update
    time: "2020-09-21T06:05:38Z"
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:status:
        f:conditions:
          k:{"type":"ContainersReady"}:
            .: {}
            f:lastProbeTime: {}
            f:lastTransitionTime: {}
            f:status: {}
            f:type: {}
          k:{"type":"Initialized"}:
            .: {}
            f:lastProbeTime: {}
            f:lastTransitionTime: {}
            f:status: {}
            f:type: {}
          k:{"type":"Ready"}:
            .: {}
            f:lastProbeTime: {}
            f:lastTransitionTime: {}
            f:status: {}
            f:type: {}
        f:containerStatuses: {}
        f:hostIP: {}
        f:phase: {}
        f:podIP: {}
        f:podIPs:
          .: {}
          k:{"ip":"10.244.1.4"}:
            .: {}
            f:ip: {}
        f:startTime: {}
    manager: kubelet
    operation: Update
    time: "2020-09-21T06:05:48Z"
  name: webapp-color
  namespace: default
  resourceVersion: "883"
  selfLink: /api/v1/namespaces/default/pods/webapp-color
  uid: ae2dee59-4b45-463a-b008-ada559e1e829
spec:
  containers:
  - env:
    - name: APP_COLOR
      value: green
    image: kodekloud/webapp-color
    imagePullPolicy: Always
    name: webapp-color
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: default-token-vrl48
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: node01
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: default-token-vrl48
    secret:
      defaultMode: 420
      secretName: default-token-vrl48
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2020-09-21T06:05:38Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2020-09-21T06:05:48Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2020-09-21T06:05:48Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2020-09-21T06:05:38Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: docker://531e55b3ee89adc7e306a9af66f1aef6dee09841f3c266a809253df45c23cfbb
    image: kodekloud/webapp-color:latest
    imageID: docker-pullable://kodekloud/webapp-color@sha256:99c3821ea49b89c7a22d3eebab5c2e1ec651452e7675af243485034a72eb1423
    lastState: {}
    name: webapp-color
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2020-09-21T06:05:48Z"
  hostIP: 172.17.0.33
  phase: Running
  podIP: 10.244.1.4
  podIPs:
  - ip: 10.244.1.4
  qosClass: BestEffort
  startTime: "2020-09-21T06:05:38Z"
master $ kubectl delete pod webapp-color
pod "webapp-color" deleted
master $ kubectl apply -f pod.yaml
pod/webapp-color created
```
5. How many ConfigMaps exist in the environment? 1
```bash
master $ kubectl get configmaps
NAME        DATA   AGE
db-config   3      20s
```
6. Identify the database host from the config map 'db-config'. `SQL01.example.com`
```bash
master $ kubectl describe configmaps db-config
Name:         db-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
DB_NAME:
----
SQL01
DB_PORT:
----
3306
DB_HOST:
----
SQL01.example.com
Events:  <none>
```
7. Create a new ConfigMap for the 'webapp-color' POD. Use the spec given on the right.
- ConfigName Name: webapp-config-map
- Data: APP_COLOR=darkblue
```bash
master $ kubectl create cm webapp-config-map --from-literal=APP_COLOR=darkblue
configmap/webapp-config-map created
```
8. Update the environment variable on the POD use the newly created ConfigMap
Note: Delete and recreate the POD. Only make the necessary changes. Do not modify the name of the Pod.
- Pod Name: webapp-color
- EnvFrom: webapp-config-map
```bash
master $ kubectl delete pod webapp-color
pod "webapp-color" deleted
master $ kubectl explain pods --recursive | grep envFrom -A3
         envFrom        <[]Object>
            configMapRef        <Object>
               name     <string>
               optional <boolean>
--
         envFrom        <[]Object>
            configMapRef        <Object>
               name     <string>
               optional <boolean>
--
         envFrom        <[]Object>
            configMapRef        <Object>
               name     <string>
               optional <boolean>
master $ vi pod.yaml
master $ grep -A5 envFrom pod.yaml
  - envFrom:
    - configMapRef:
       name: webapp-config-map
    image: kodekloud/webapp-color
    imagePullPolicy: Always
    name: webapp-color
master $ kubectl apply -f pod.yaml
pod/webapp-color created
```
