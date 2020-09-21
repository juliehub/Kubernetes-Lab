1. How many PODs exist on the system? 1
in the current(default) namespace
```bash
master $ kubectl get pods
NAME             READY   STATUS              RESTARTS   AGE
ubuntu-sleeper   0/1     ContainerCreating   0          27s
```
2. What is the command used to run the pod 'ubuntu-sleeper'?
```bash
master $ kubectl describe pod ubuntu-sleeper |grep -iA5 command
    Command:
      sleep
      4800
    State:          Running
      Started:      Mon, 21 Sep 2020 03:33:21 +0000
    Ready:          True
```
3. Create a pod with the ubuntu image to run a container to sleep for 5000 seconds. Modify the file ubuntu-sleeper-2.yaml.
Note: Only make the necessary changes. Do not modify the name.
- Pod Name: ubuntu-sleeper-2
- Command: sleep 5000
```bash
master $ cat ubuntu-sleeper-2.yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-2
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["sleep", "5000"]
master $ kubectl apply -f ubuntu-sleeper-2.yaml
pod/ubuntu-sleeper-2 created
master $ kubectl get pods
NAME               READY   STATUS    RESTARTS   AGE
ubuntu-sleeper     1/1     Running   0          13m
ubuntu-sleeper-2   1/1     Running   0          8s
master $ kubectl describe pod ubuntu-sleeper-2 |grep -iA5 command
    Command:
      sleep
      5000
    State:          Running
      Started:      Mon, 21 Sep 2020 03:46:17 +0000
    Ready:          True
```
4. Create a pod using the file named 'ubuntu-sleeper-3.yaml'. There is something wrong with it. Try to fix it!
Note: Only make the necessary changes. Do not modify the name.
Pod Name: ubuntu-sleeper-3
Command: sleep 1200
```bash
master $ cat ubuntu-sleeper-3.yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-3
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
      - "sleep"
      - "1200"
master $ kubectl apply -f ubuntu-sleeper-3.yaml
pod/ubuntu-sleeper-3 created
master $ kubectl get pods
NAME               READY   STATUS    RESTARTS   AGE
ubuntu-sleeper     1/1     Running   0          16m
ubuntu-sleeper-2   1/1     Running   0          3m10s
ubuntu-sleeper-3   1/1     Running   0          6s
master $ kubectl describe pod ubuntu-sleeper-3 |grep -iA5 command
    Command:
      sleep
      1200
    State:          Running
      Started:      Mon, 21 Sep 2020 03:49:20 +0000
    Ready:          True
```
5. Update pod 'ubuntu-sleeper-3' to sleep for 2000 seconds.
```bash
master $ kubectl delete pod ubuntu-sleeper-3
pod "ubuntu-sleeper-3" deleted
master $ vi ubuntu-sleeper-3.yaml
master $ cat ubuntu-sleeper-3.yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-3
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
      - "sleep"
      - "2000"
master $ kubectl apply -f ubuntu-sleeper-3.yaml
pod/ubuntu-sleeper-3 created
```
6. Inspect the file 'Dockerfile' given at /root/webapp-color. What command is run at container startup? `python app.py`
```bash
master $ cat Dockerfile
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]
```
7. Inspect the file 'Dockerfile2' given at /root/webapp-color. What command is run at container startup?
`python app.py --color red`
```bash
master $ cat Dockerfile2
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
```
8. Inspect the two files under directory 'webapp-color-2'. What command is run at container startup?
Assume the image was created from the Dockerfile in this folder
`--color green`
```bash
master $ cat Dockerfile2
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
master $ cat webapp-color-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp-green
  labels:
      name: webapp-green
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    command: ["--color","green"]
```
9. Inspect the two files under directory 'webapp-color-3'. What command is run at container startup?
Assume the image was created from the Dockerfile in this folder
`python app.py --color pink`
```bash
master $ cat Dockerfile2
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
master $ cat webapp-color-pod-2.yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp-green
  labels:
      name: webapp-green
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    command: ["python", "app.py"]
    args: ["--color", "pink"]
```
10. Create a pod with the given specifications. By default it displays a 'blue' background. Set the given command line arguments to change it to 'green'
- Pod Name: webapp-green
- Image: kodekloud/webapp-color
- Command line arguments: --color=green
```bash
master $ kubectl run webapp-green --image=kodekloud/webapp-color --restart=Never --dry-run=client -o yaml > pod.yaml
master $ vi pod.yaml
master $ cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: webapp-green
  name: webapp-green
spec:
  containers:
  - image: kodekloud/webapp-color
    name: webapp-green
    args: ["--color=green"]
  dnsPolicy: ClusterFirst
  restartPolicy: Never
master $ kubectl create -f pod.yaml
pod/webapp-green created
master $ kubectl describe pod webapp-green |grep -iA5 commandmaster $ kubectl describe pod webapp-green |grep -iA5 args
    Args:
      --color=green
    State:          Running
      Started:      Mon, 21 Sep 2020 04:12:33 +0000
    Ready:          True
    Restart Count:  0
```