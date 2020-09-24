1. Identify the certificate file used for the kube-api server
`/etc/kubernetes/pki/apiserver.crt`
```bash
master $ cat /etc/kubernetes/manifests/kube-apiserver.yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 172.17.0.9:6443
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --advertise-address=172.17.0.9
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --enable-admission-plugins=NodeRestriction
    - --enable-bootstrap-token-auth=true
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
    - --etcd-servers=https://127.0.0.1:2379
    - --insecure-port=0
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
    - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
    - --requestheader-allowed-names=front-proxy-client
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --requestheader-extra-headers-prefix=X-Remote-Extra-
    - --requestheader-group-headers=X-Remote-Group
    - --requestheader-username-headers=X-Remote-User
    - --secure-port=6443
    - --service-account-key-file=/etc/kubernetes/pki/sa.pub
    - --service-cluster-ip-range=10.96.0.0/12
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
    image: k8s.gcr.io/kube-apiserver:v1.18.0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 172.17.0.9
        path: /healthz
        port: 6443
        scheme: HTTPS
      initialDelaySeconds: 15
      timeoutSeconds: 15
    name: kube-apiserver
    resources:
      requests:
        cpu: 250m
    volumeMounts:
    - mountPath: /etc/ssl/certs
      name: ca-certs
      readOnly: true
    - mountPath: /etc/ca-certificates
      name: etc-ca-certificates
      readOnly: true
    - mountPath: /etc/kubernetes/pki
      name: k8s-certs
      readOnly: true
    - mountPath: /usr/local/share/ca-certificates
      name: usr-local-share-ca-certificates
      readOnly: true
    - mountPath: /usr/share/ca-certificates
      name: usr-share-ca-certificates
      readOnly: true
  hostNetwork: true
  priorityClassName: system-cluster-critical
  volumes:
  - hostPath:
      path: /etc/ssl/certs
      type: DirectoryOrCreate
    name: ca-certs
  - hostPath:
      path: /etc/ca-certificates
      type: DirectoryOrCreate
    name: etc-ca-certificates
  - hostPath:
      path: /etc/kubernetes/pki
      type: DirectoryOrCreate
    name: k8s-certs
  - hostPath:
      path: /usr/local/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-local-share-ca-certificates
  - hostPath:
      path: /usr/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-share-ca-certificates
status: {}
```
2. Identify the Certificate file used to authenticate kube-apiserver as a client to ETCD Server
`/etc/kubernetes/pki/apiserver-etcd-client.crt`
```bash
master $ cat /etc/kubernetes/manifests/kube-apiserver.yaml  |grep crt
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
```
3. Identify the key used to authenticate kubeapi-server to the kubelet server
`/etc/kubernetes/pki/apiserver-kubelet-client.key`
```bash
master $ cat /etc/kubernetes/manifests/kube-apiserver.yaml  |grep key
    - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
    - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
    - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
    - --service-account-key-file=/etc/kubernetes/pki/sa.pub
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
```
4. Identify the ETCD Server Certificate used to host ETCD server
`/etc/kubernetes/pki/etcd/server.crt`
```bash
master $ cat /etc/kubernetes/manifests/etcd.yaml|grep crt
    - --cert-file=/etc/kubernetes/pki/etcd/server.crt
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```
5. Identify the ETCD Server CA Root Certificate used to serve ETCD Server
ETCD can have its own CA. So this may be a different CA certificate than the one used by kube-api server.
`/etc/kubernetes/pki/etcd/ca.crt`

6. What is the Common Name (CN) configured on the Kube API Server Certificate?
OpenSSL Syntax: openssl x509 -in file-path.crt -text -noout
`kube-apiserver`
```bash
master $ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 7673492106836239870 (0x6a7db86a4ebce5fe)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Sep 24 07:05:48 2020 GMT
            Not After : Sep 24 07:05:49 2021 GMT
        Subject: CN = kube-apiserver
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
```
7. What is the name of the CA who issued the Kube API Server Certificate?
`CN = kubernetes`

8. Which of the below alternate names is not configured on the Kube API Server Certificate?
`kubernetes-master`
```bash
X509v3 Subject Alternative Name:
                DNS:master, DNS:kubernetes, DNS:kubernetes.default, DNS:kubernetes.default.svc, DNS:kubernetes.default.svc.cluster.local, IP Address:10.96.0.1, IP Address:172.17.0.9`
```
9. What is the Common Name (CN) configured on the ETCD Server certificate?
`CN = master`
```bash
master $ openssl x509 -in /etc/kubernetes/pki/etcd/server.crt -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 3373792112044014866 (0x2ed21e0d0065fd12)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = etcd-ca
        Validity
            Not Before: Sep 24 07:05:50 2020 GMT
            Not After : Sep 24 07:05:50 2021 GMT
        Subject: CN = master
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
```
10. How long, from the issued date, is the Kube-API Server Certificate valid for? ` year
File: /etc/kubernetes/pki/apiserver.crt

```bash
master $ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 7673492106836239870 (0x6a7db86a4ebce5fe)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Sep 24 07:05:48 2020 GMT
            Not After : Sep 24 07:05:49 2021 GMT
        Subject: CN = kube-apiserver
```
11. How long, from the issued date, is the Root CA Certificate valid for? 10 year
File: /etc/kubernetes/pki/ca.crt
```bash
master $ openssl x509 -in /etc/kubernetes/pki/ca.crt -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 0 (0x0)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Sep 24 07:05:48 2020 GMT
            Not After : Sep 22 07:05:48 2030 GMT
        Subject: CN = kubernetes
```
12. Kubectl suddenly stops responding to your commands. Check it out! Someone recently modified the /etc/kubernetes/manifests/etcd.yaml file
You are asked to investigate and fix the issue. Once you fix the issue wait for sometime for kubectl to respond. Check the logs of the ETCD container.
```bash
master $ cat /etc/kubernetes/manifests/etcd.yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/etcd.advertise-client-urls: https://172.17.0.9:2379
  creationTimestamp: null
  labels:
    component: etcd
    tier: control-plane
  name: etcd
  namespace: kube-system
spec:
  containers:
  - command:
    - etcd
    - --advertise-client-urls=https://172.17.0.9:2379
    - --cert-file=/etc/kubernetes/pki/etcd/server-certificate.crt
    - --client-cert-auth=true
    - --data-dir=/var/lib/etcd
    - --initial-advertise-peer-urls=https://172.17.0.9:2380
    - --initial-cluster=master=https://172.17.0.9:2380
    - --key-file=/etc/kubernetes/pki/etcd/server.key
    - --listen-client-urls=https://127.0.0.1:2379,https://172.17.0.9:2379
    - --listen-metrics-urls=http://127.0.0.1:2381
    - --listen-peer-urls=https://172.17.0.9:2380
    - --name=master
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
    - --peer-client-cert-auth=true
    - --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    - --snapshot-count=10000
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    image: k8s.gcr.io/etcd:3.4.3-0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 127.0.0.1
        path: /health
        port: 2381
        scheme: HTTP
      initialDelaySeconds: 15
      timeoutSeconds: 15
    name: etcd
    resources: {}
    volumeMounts:
    - mountPath: /var/lib/etcd
      name: etcd-data
    - mountPath: /etc/kubernetes/pki/etcd
      name: etcd-certs
  hostNetwork: true
  priorityClassName: system-cluster-critical
  volumes:
  - hostPath:
      path: /etc/kubernetes/pki/etcd
      type: DirectoryOrCreate
    name: etcd-certs
  - hostPath:
      path: /var/lib/etcd
      type: DirectoryOrCreate
    name: etcd-data
status: {}
master $ cat /etc/kubernetes/pki/etcd/server-certificate.crt
cat: /etc/kubernetes/pki/etcd/server-certificate.crt: No such file or directory
```
```bash
master $ vi /etc/kubernetes/manifests/etcd.yaml
master $ cat /etc/kubernetes/manifests/etcd.yaml | grep crt
    - --cert-file=/etc/kubernetes/pki/etcd/server.crt
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
master $
```
13. The kube-api server stopped again! Check it out. Inspect the kube-api server logs and identify the root cause and fix the issue.
Run docker ps -a command to identify the kube-api server container. Run docker logs container-id command to view the logs.
```bash
master $ docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS   NAMES
3b84a0908cbf        d3e55153f52f           "kube-controller-man…"   3 minutes ago       Up 3 minutes   k8s_kube-controller-manager_kube-controller-manager-master_kube-system_f9b9c6969be80756638e9cf4927b5881_2
703086f5c402        k8s.gcr.io/pause:3.2   "/pause"                 3 minutes ago       Up 3 minutes   k8s_POD_kube-apiserver-master_kube-system_ed557ed5884d6fca661fc51d21f8b870_0
21e126212186        303ce5db0e90           "etcd --advertise-cl…"   4 minutes ago       Up 4 minutes   k8s_etcd_etcd-master_kube-system_96157c01f5e90e80fb936c9ae4c13435_1
8b8c5386b5ec        k8s.gcr.io/pause:3.2   "/pause"                 4 minutes ago       Up 4 minutes   k8s_POD_etcd-master_kube-system_96157c01f5e90e80fb936c9ae4c13435_1
bd6375680e42        a31f78c7c8ce           "kube-scheduler --au…"   8 minutes ago       Up 8 minutes   k8s_kube-scheduler_kube-scheduler-master_kube-system_5795d0c442cb997ff93c49feeb9f6386_1
8c475f460bf7        67da37a9a360           "/coredns -conf /etc…"   42 minutes ago      Up 42 minutes   k8s_coredns_coredns-66bff467f8-x9fvv_kube-system_b5e300ba-ccf6-4d95-afbb-e255e18a7a10_0
0b41d99b3e6c        k8s.gcr.io/pause:3.2   "/pause"                 42 minutes ago      Up 42 minutes   k8s_POD_coredns-66bff467f8-x9fvv_kube-system_b5e300ba-ccf6-4d95-afbb-e255e18a7a10_0
1510724f4500        67da37a9a360           "/coredns -conf /etc…"   42 minutes ago      Up 42 minutes   k8s_coredns_coredns-66bff467f8-8zlcg_kube-system_86d4387d-fb1f-4f54-8b4d-052289a9ce43_0
8065dcdb296b        k8s.gcr.io/pause:3.2   "/pause"                 42 minutes ago      Up 42 minutes   k8s_POD_coredns-66bff467f8-8zlcg_kube-system_86d4387d-fb1f-4f54-8b4d-052289a9ce43_0
86f0ad081f6e        4e9f801d2217           "/opt/bin/flanneld -…"   42 minutes ago      Up 42 minutes   k8s_kube-flannel_kube-flannel-ds-amd64-6qb8c_kube-system_5faafc95-edad-4ef7-b51e-9f33eba6decf_0
56269d0080a9        43940c34f24f           "/usr/local/bin/kube…"   42 minutes ago      Up 42 minutes   k8s_kube-proxy_kube-proxy-mbwg7_kube-system_41de181b-ca95-40e3-bc9a-61cb969548e5_0
415b1a675c96        k8s.gcr.io/pause:3.2   "/pause"                 42 minutes ago      Up 42 minutes   k8s_POD_kube-proxy-mbwg7_kube-system_41de181b-ca95-40e3-bc9a-61cb969548e5_0
5376c392b3a9        k8s.gcr.io/pause:3.2   "/pause"                 42 minutes ago      Up 42 minutes   k8s_POD_kube-flannel-ds-amd64-6qb8c_kube-system_5faafc95-edad-4ef7-b51e-9f33eba6decf_0
f6d0a5599601        k8s.gcr.io/pause:3.2   "/pause"                 43 minutes ago      Up 43 minutes   k8s_POD_kube-scheduler-master_kube-system_5795d0c442cb997ff93c49feeb9f6386_0
a529fc8ea040        k8s.gcr.io/pause:3.2   "/pause"                 43 minutes ago      Up 43 minutes   k8s_POD_kube-controller-manager-master_kube-system_f9b9c6969be80756638e9cf4927b5881_0
```
```bash
master $ cat /etc/kubernetes/manifests/kube-apiserver.yaml |grep crt
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --etcd-cafile=/etc/kubernetes/pki/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
```
```bash
master $ vi /etc/kubernetes/manifests/kube-apiserver.yaml
master $ cat /etc/kubernetes/manifests/kube-apiserver.yaml |grep crt
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
```