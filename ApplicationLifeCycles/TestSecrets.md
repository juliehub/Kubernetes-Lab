1. How many Secrets exist on the system?
in the current(default) namespace
```bash
master $ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
default-token-cctc4   kubernetes.io/service-account-token   3      95s
```
2. How many secrets are defined in the 'default-token' secret? 3

3. What is the type of the 'default-token' secret? `kubernetes.io/service-account-token`
```bash
master $ kubectl describe secret default-token-cctc4
Name:         default-token-cctc4
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: c8733dc2-bf95-4825-b3df-8dc1f2800750

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  7 bytes
token:      eyJh...
```
4. Which of the following is not a secret data defined in 'default-token' secret? type
- namespace
- token
- type
- ca.crt

5. We are going to deploy an application with the below architecture: webapp connects to MySQL.
We have already deployed the required pods and services. Check out the pods and services created.

6. The reason the application is failed is because we have not created the secrets yet. Create a new Secret named 'db-secret' with the data given(on the right).
- Secret Name: db-secret
- Secret 1: DB_Host=sql01
- Secret 2: DB_User=root
- Secret 3: DB_Password=password123
```bash
master $ kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123
secret/db-secret created
master $ kubectl get secretsNAME                  TYPE                                  DATA   AGE
db-secret             Opaque                                3      70s
default-token-cctc4   kubernetes.io/service-account-token   3      14m
```

7. Configure webapp-pod to load environment variables from the newly created secret.
Delete and recreate the pod if required.
- Pod name: webapp-pod
- Image name: kodekloud/simple-webapp-mysql
- Env From: Secret=db-secret
```bash
master $ kubectl get pod webapp-pod -o yaml > pod.yaml
master $ kubectl delete pod webapp-pod
pod "webapp-pod" deleted
master $ kubectl explain pods --recursive | grep -A8 envFrom
         envFrom        <[]Object>
            configMapRef        <Object>
               name     <string>
               optional <boolean>
            prefix      <string>
            secretRef   <Object>
               name     <string>
               optional <boolean>
         image  <string>
master $ cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: webapp-pod
  name: webapp-pod
  namespace: default
spec:
  containers:
  - image: kodekloud/simple-webapp-mysql
    imagePullPolicy: Always
    name: webapp
    envFrom:
    - secretRef:
       name: db-secret
master $ kubectl apply -f pod.yaml
pod/webapp-pod created
```




