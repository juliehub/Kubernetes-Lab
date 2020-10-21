1. How many nodes are part of this cluster?
Including the master and worker nodes.
Answer: 2
```bash
master $ kubectl get nodes
NAME     STATUS   ROLES    AGE   VERSION
master   Ready    master   23m   v1.18.0
node01   Ready    <none>   22m   v1.18.0
```
2. What is the network interface configured for cluster connectivity on the master node? node-to-node communication.
Answer: `ens3`
```bash
master $ ip link1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 02:42:ac:11:00:0c brd ff:ff:ff:ff:ff:ff
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:de:e9:37:ca brd ff:ff:ff:ff:ff:ff
4: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 1a:05:b9:74:0e:9f brd ff:ff:ff:ff:ff:ff
5: vethe1dd6d99@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master cni0 state UP mode DEFAULT group default
    link/ether a2:97:4a:f0:3e:06 brd ff:ff:ff:ff:ff:ff link-netnsid 0
```
```bash
master $ ifconfig -a
cni0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.244.0.1  netmask 255.255.255.0  broadcast 0.0.0.0
        inet6 fe80::1805:b9ff:fe74:e9f  prefixlen 64  scopeid 0x20<link>
        ether 1a:05:b9:74:0e:9f  txqueuelen 1000  (Ethernet)
        RX packets 3662  bytes 248647 (248.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4012  bytes 1569225 (1.5 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.18.0.1  netmask 255.255.255.0  broadcast 172.18.0.255
        ether 02:42:de:e9:37:ca  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.12  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:acff:fe11:c  prefixlen 64  scopeid 0x20<link>
        ether 02:42:ac:11:00:0c  txqueuelen 1000  (Ethernet)
        RX packets 45172  bytes 44042495 (44.0 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 23680  bytes 16193099 (16.1 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 288501  bytes 66282553 (66.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 288501  bytes 66282553 (66.2 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

vethe1dd6d99: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::a097:4aff:fef0:3e06  prefixlen 64  scopeid 0x20<link>
        ether a2:97:4a:f0:3e:06  txqueuelen 0  (Ethernet)
        RX packets 3662  bytes 299915 (299.9 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4027  bytes 1570295 (1.5 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
Another way
```bash
master $ cat /etc/network/interfaces
# ifupdown has been replaced by netplan(5) on this system.  See
# /etc/netplan for current configuration.
# To re-enable ifupdown on this system, you can run:
#    sudo apt install ifupdown
auto lo
iface lo inet loopback

auto ens3
allow-hotplug ens3
iface ens3 inet dhcp
```
3. What is the IP address assigned to the master node on this interface?
Answer: `172.17.0.12`
```bash
master $ kubectl get nodes -o wide
NAME     STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION       CONTAINER-RUNTIME
master   Ready    master   30m   v1.18.0   172.17.0.12   <none>        Ubuntu 18.04.4 LTS   4.15.0-109-generic   docker://19.3.6
node01   Ready    <none>   30m   v1.18.0   172.17.0.16   <none>        Ubuntu 18.04.4 LTS   4.15.0-109-generic   docker://19.3.6
```
4. What is the MAC address of the interface on the master node?
Answer: `02:42:ac:11:00:0c`
```bash
master $ ip link show ens3
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 02:42:ac:11:00:0c brd ff:ff:ff:ff:ff:ff
```
or
```bash
master $ ifconfig ens3
ens3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.12  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:acff:fe11:c  prefixlen 64  scopeid 0x20<link>
        ether 02:42:ac:11:00:0c  txqueuelen 1000  (Ethernet)
        RX packets 51832  bytes 44732063 (44.7 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 31541  bytes 21989734 (21.9 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
5. What is the IP address assigned to node01? `172.17.0.16`

6. What is the MAC address assigned to node01? `02:42:ac:11:00:10`
```bash
master $ ssh node01 ifconfig ens3
Warning: Permanently added 'node01,172.17.0.16' (ECDSA) to the list of known hosts.
ens3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.16  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:acff:fe11:10  prefixlen 64  scopeid 0x20<link>
        ether 02:42:ac:11:00:10  txqueuelen 1000  (Ethernet)
        RX packets 122400  bytes 141657595 (141.6 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 50240  bytes 5390243 (5.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
or 
```bash
master $ arp node01
Address                  HWtype  HWaddress           Flags Mask            Iface
node01                   ether   02:42:ac:11:00:10   C                     ens3
```
7. We use Docker as our container runtime. What is the interface/bridge created by Docker on this host? `docker0`
```bash
master $ ssh node01 ifconfig -a
cni0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.244.2.1  netmask 255.255.255.0  broadcast 0.0.0.0
        inet6 fe80::6c98:adff:fe01:ae6  prefixlen 64  scopeid 0x20<link>
        ether 6e:98:ad:01:0a:e6  txqueuelen 1000  (Ethernet)
        RX packets 11895  bytes 1176861 (1.1 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 11882  bytes 9339278 (9.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.18.0.1  netmask 255.255.255.0  broadcast 172.18.0.255
        ether 02:42:64:bc:ab:bc  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.16  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:acff:fe11:10  prefixlen 64  scopeid 0x20<link>
        ether 02:42:ac:11:00:10  txqueuelen 1000  (Ethernet)
        RX packets 124876  bytes 142536670 (142.5 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 52829  bytes 5689970 (5.6 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 540  bytes 41808 (41.8 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 540  bytes 41808 (41.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth2a263010: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::a412:3bff:fe2c:c937  prefixlen 64  scopeid 0x20<link>
        ether a6:12:3b:2c:c9:37  txqueuelen 0  (Ethernet)
        RX packets 5813  bytes 846310 (846.3 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5510  bytes 6839125 (6.8 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth76ce3ed3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::82c:3bff:fe91:252d  prefixlen 64  scopeid 0x20<link>
        ether 0a:2c:3b:91:25:2d  txqueuelen 0  (Ethernet)
        RX packets 6082  bytes 497081 (497.0 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 6420  bytes 2503581 (2.5 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
8. What is the state of the interface docker0? `DOWN`
```bash
master $ ssh node01 ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 02:42:ac:11:00:10 brd ff:ff:ff:ff:ff:ff
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:64:bc:ab:bc brd ff:ff:ff:ff:ff:ff
4: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 6e:98:ad:01:0a:e6 brd ff:ff:ff:ff:ff:ff
5: veth2a263010@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master cni0 state UP mode DEFAULT group default
    link/ether a6:12:3b:2c:c9:37 brd ff:ff:ff:ff:ff:ff link-netnsid 0
6: veth76ce3ed3@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master cni0 state UP mode DEFAULT group default
    link/ether 0a:2c:3b:91:25:2d brd ff:ff:ff:ff:ff:ff link-netnsid 1
```
9. If you were to ping google from the master node, which route does it take?
What is the IP address of the Default Gateway? `172.17.0.1`
```bash
master $ ip route
default via 172.17.0.1 dev ens3
10.244.0.0/24 dev cni0 proto kernel scope link src 10.244.0.1
10.244.2.0/24 via 172.17.0.16 dev ens3
172.17.0.0/16 dev ens3 proto kernel scope link src 172.17.0.12
172.18.0.0/24 dev docker0 proto kernel scope link src 172.18.0.1 linkdown
```
10. What is the port the kube-scheduler is listening on in the master node? `10251`
```bash
master $ netstat -nplt |grep scheduler
tcp        0      0 127.0.0.1:10259         0.0.0.0:*               LISTEN      2284/kube-scheduler
tcp6       0      0 :::10251                :::*                    LISTEN      2284/kube-scheduler
```
11. Notice that ETCD is listening on two ports. Which of these have more client connections established? `2379`
```bash
master $ netstat -nplt  |grep etcd
tcp        0      0 127.0.0.1:2381          0.0.0.0:*               LISTEN      2308/etcd
tcp        0      0 172.17.0.12:2379        0.0.0.0:*               LISTEN      2308/etcd
tcp        0      0 127.0.0.1:2379          0.0.0.0:*               LISTEN      2308/etcd
tcp        0      0 172.17.0.12:2380        0.0.0.0:*               LISTEN      2308/etcd
```
That's because 2379 is the port of ETCD to which all control plane components connect to. 2380 is only for etcd peer-to-peer connectivity. When you have multiple master nodes. In this case we don't.