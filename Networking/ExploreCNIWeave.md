1. Inspect the kubelet service and identify the network plugin configured for Kubernetes.
Run `ps -aux | grep kubelet` command
Answer: `cni`
```bash
controlplane $ ps -aux | grep kubelet
root      2343 10.7 18.6 1095244 380560 ?      Ssl  22:57   0:15 kube-apiserver --advertise-address=172.17.0.44 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/etc/kubernetes/pki/ca.crt --enable-admission-plugins=NodeRestriction --enable-bootstrap-token-auth=true --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key --etcd-servers=https://127.0.0.1:2379 --insecure-port=0 --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6443 --service-account-key-file=/etc/kubernetes/pki/sa.pub --service-cluster-ip-range=10.96.0.0/12 --tls-cert-file=/etc/kubernetes/pki/apiserver.crt --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
root      4582  3.7  4.6 1782940 93948 ?       Ssl  22:58   0:03 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --cgroup-driver=systemd --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.1 --cni-bin-dir=/opt/cni/bin
root      6455  0.0  0.0  14728  1088 pts/0    S+   22:59   0:00 grep --color=auto kubelet
```
2. What is the path configured with all binaries of CNI supported plugins?
Answer: `/opt/cni/bin`
```bash
controlplane $ ps -aux | grep kubelet | grep cni
root      4582  3.5  4.7 1782940 97288 ?       Ssl  22:58   0:12 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --cgroup-driver=systemd --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.1 --cni-bin-dir=/opt/cni/bin
```
3. Identify which of the below plugins is not available in the list of available CNI plugins on this host
Answer: `cisco`
```bash
controlplane $ ls /opt/cni/bin
bandwidth  dhcp      host-device  ipvlan    macvlan  ptp  static  vlan        weave-net
bridge     firewall  host-local   loopback  portmap  sbr  tuning  weave-ipam  weave-plugin-2.7.0
```
4. What is the CNI plugin configured to be used on this kubernetes cluster?
Answer: `weave`
```bash
controlplane $ ls /etc/cni/net.d/
10-weave.conflist
```
5. What binary executable file will be run by kubelet after a container and its associated namespace are created.
Answer: `weeve-net`
```bash
controlplane $ cat /etc/cni/net.d/10-weave.conflist
{
    "cniVersion": "0.3.0",
    "name": "weave",
    "plugins": [
        {
            "name": "weave",
            "type": "weave-net",
            "hairpinMode": true
        },
        {
            "type": "portmap",
            "capabilities": {"portMappings": true},
            "snat": true
        }
    ]
}
```
