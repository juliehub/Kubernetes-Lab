1. A new member akshay joined our team. He requires access to our cluster. The Certificate Signing Request is at the /root location. Inspect it
```bash
master $ kubectl get csr
NAME        AGE     SIGNERNAME                                    REQUESTOR                 CONDITION
csr-b489f   4m20s   kubernetes.io/kube-apiserver-client-kubelet   system:node:master        Approved,Issued
csr-gcrk7   3m57s   kubernetes.io/kube-apiserver-client-kubelet   system:bootstrap:96771a   Approved,Issued
```
2. Create a CertificateSigningRequest object with the name akshay with the contents of the akshay.csr file
```bash
master $ openssl req -new -key akshay.key -subj "/CN=akshay" -out akshay.csr
master $ cat akshay.csr
-----BEGIN CERTIFICATE REQUEST-----
MIICVjCCAT4CAQAwETEPMA0GA1UEAwwGYWtzaGF5MIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEAw1Kjm1bCP4yci30dMZdl1BZNehdqNT2v2vylP8DXIbG0
YCa0an7DM/rhIn+R6FBEvcrD3SEhNJlFljzeTJMTeP4s1Wg6FiBJmlMEnIoL6lQS
GoZjNojIA8JBQmC8hmZ8qANx73c3RCmDMgm2pdqgixZi5fA9e/ZKsWzYoLfHYO8T
NUJcaNz4yWcli7JHWpWjICqCzdgpCx7+tpqLDdDFAP7LcOoQhfeB3WMAFun3gE5b
KGJjDGTM+nOF2k3Aq5y6HFHdWgXRxnFkhcUUbNVqR+CSlVUu40lTargI8OqhtATh
FXESvHZorO/g+TVrHjPZoWa0Ryxwbj9RaoaxO8O0BQIDAQABoAAwDQYJKoZIhvcN
AQELBQADggEBABqn4VGhxR1gQ0xNEIRvWo7UVEVe2OjTSo6TOp4fOl2Ua54lnueI
jNX3bvuuOxJY6ILTnthNymEEmBEXbLP9MQPUSu1YI8XuGV0hmeZBvUZ1E9QY1M85
UQJHX9oPsRUX3EhpthvLAU2EhNzeeTSWQQzb/iB8rL4mvLacaFQYguLk0HPwEdy/
ZUse1GlvaMiQ1XY/LDQDatnLDwvwibdu9RhBqrScIGKpzXq3eww8R2Rj209dv0A0
esDIyqXYYjLJVSAYUh+ZnafNb0serDgTV28yltkRttd7UsFJPJ7pa0ZqX5EMZAAx
Ycb0kaivpZbkwn0CBleyAG/M2lipFVHcNZg=
-----END CERTIFICATE REQUEST-----
master $ cat akshay.csr | base64
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBN
QTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJD
Z0tDQVFFQXcxS2ptMWJDUDR5Y2kzMGRNWmRsMUJaTmVoZHFOVDJ2MnZ5bFA4RFhJYkcwCllDYTBh
bjdETS9yaEluK1I2RkJFdmNyRDNTRWhOSmxGbGp6ZVRKTVRlUDRzMVdnNkZpQkptbE1FbklvTDZs
UVMKR29aak5vaklBOEpCUW1DOGhtWjhxQU54NzNjM1JDbURNZ20ycGRxZ2l4Wmk1ZkE5ZS9aS3NX
ellvTGZIWU84VApOVUpjYU56NHlXY2xpN0pIV3BXaklDcUN6ZGdwQ3g3K3RwcUxEZERGQVA3TGNP
b1FoZmVCM1dNQUZ1bjNnRTViCktHSmpER1RNK25PRjJrM0FxNXk2SEZIZFdnWFJ4bkZraGNVVWJO
VnFSK0NTbFZVdTQwbFRhcmdJOE9xaHRBVGgKRlhFU3ZIWm9yTy9nK1RWckhqUFpvV2EwUnl4d2Jq
OVJhb2F4TzhPMEJRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBQnFuNFZHaHhS
MWdRMHhORUlSdldvN1VWRVZlMk9qVFNvNlRPcDRmT2wyVWE1NGxudWVJCmpOWDNidnV1T3hKWTZJ
TFRudGhOeW1FRW1CRVhiTFA5TVFQVVN1MVlJOFh1R1YwaG1lWkJ2VVoxRTlRWTFNODUKVVFKSFg5
b1BzUlVYM0VocHRodkxBVTJFaE56ZWVUU1dRUXpiL2lCOHJMNG12TGFjYUZRWWd1TGswSFB3RWR5
LwpaVXNlMUdsdmFNaVExWFkvTERRRGF0bkxEd3Z3aWJkdTlSaEJxclNjSUdLcHpYcTNld3c4UjJS
ajIwOWR2MEEwCmVzREl5cVhZWWpMSlZTQVlVaCtabmFmTmIwc2VyRGdUVjI4eWx0a1J0dGQ3VXNG
SlBKN3BhMFpxWDVFTVpBQXgKWWNiMGthaXZwWmJrd24wQ0JsZXlBRy9NMmxpcEZWSGNOWmc9Ci0t
LS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
```
```bash
master $ cat akshay-csr.yaml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  groups:
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQXcxS2ptMWJDUDR5Y2kzMGRNWmRsMUJaTmVoZHFOVDJ2MnZ5bFA4RFhJYkcwCllDYTBhbjdETS9yaEluK1I2RkJFdmNyRDNTRWhOSmxGbGp6ZVRKTVRlUDRzMVdnNkZpQkptbE1FbklvTDZsUVMKR29aak5vaklBOEpCUW1DOGhtWjhxQU54NzNjM1JDbURNZ20ycGRxZ2l4Wmk1ZkE5ZS9aS3NXellvTGZIWU84VApOVUpjYU56NHlXY2xpN0pIV3BXaklDcUN6ZGdwQ3g3K3RwcUxEZERGQVA3TGNPb1FoZmVCM1dNQUZ1bjNnRTViCktHSmpER1RNK25PRjJrM0FxNXk2SEZIZFdnWFJ4bkZraGNVVWJOVnFSK0NTbFZVdTQwbFRhcmdJOE9xaHRBVGgKRlhFU3ZIWm9yTy9nK1RWckhqUFpvV2EwUnl4d2JqOVJhb2F4TzhPMEJRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBQnFuNFZHaHhSMWdRMHhORUlSdldvN1VWRVZlMk9qVFNvNlRPcDRmT2wyVWE1NGxudWVJCmpOWDNidnV1T3hKWTZJTFRudGhOeW1FRW1CRVhiTFA5TVFQVVN1MVlJOFh1R1YwaG1lWkJ2VVoxRTlRWTFNODUKVVFKSFg5b1BzUlVYM0VocHRodkxBVTJFaE56ZWVUU1dRUXpiL2lCOHJMNG12TGFjYUZRWWd1TGswSFB3RWR5LwpaVXNlMUdsdmFNaVExWFkvTERRRGF0bkxEd3Z3aWJkdTlSaEJxclNjSUdLcHpYcTNld3c4UjJSajIwOWR2MEEwCmVzREl5cVhZWWpMSlZTQVlVaCtabmFmTmIwc2VyRGdUVjI4eWx0a1J0dGQ3VXNGSlBKN3BhMFpxWDVFTVpBQXgKWWNiMGthaXZwWmJrd24wQ0JsZXlBRy9NMmxpcEZWSGNOWmc9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  usages:
  - digital signature
  - key encipherment
  - server auth
master $ kubectl create -f  akshay-csr.yaml
certificatesigningrequest.certificates.k8s.io/akshay created
master $ kubectl get csr
NAME        AGE   SIGNERNAME                                    REQUESTOR                 CONDITION
akshay      5s    kubernetes.io/legacy-unknown                  kubernetes-admin          Pending
csr-b489f   22m   kubernetes.io/kube-apiserver-client-kubelet   system:node:master        Approved,Issued
csr-gcrk7   22m   kubernetes.io/kube-apiserver-client-kubelet   system:bootstrap:96771a   Approved,Issued
```
3. What is the Condition of the newly created Certificate Signing Request object?
`Pending`

4. Approve the CSR Request
```bash
master $ kubectl certificate approve akshay
certificatesigningrequest.certificates.k8s.io/akshay approved
```

5. How many CSR requests are available on the cluster?
Including approved and pending

6. Who requested the csr-* request? `master node`
```bash
master $ kubectl get  csr
NAME        AGE   SIGNERNAME                                    REQUESTOR                 CONDITION
akshay      15m   kubernetes.io/legacy-unknown                  kubernetes-admin          Approved,Issued
csr-b489f   37m   kubernetes.io/kube-apiserver-client-kubelet   system:node:master        Approved,Issued
csr-gcrk7   37m   kubernetes.io/kube-apiserver-client-kubelet   system:bootstrap:96771a   Approved,Issued
```

7. During a routine check you realized that there is a new CSR request in place. What is the name of this request? agent-smith
```bash
master $ kubectl get  csr
NAME          AGE    SIGNERNAME                                    REQUESTOR                 CONDITION
agent-smith   2m3s   kubernetes.io/legacy-unknown                  agent-x                   Pending
akshay        24m    kubernetes.io/legacy-unknown                  kubernetes-admin          Approved,Issued
csr-b489f     46m    kubernetes.io/kube-apiserver-client-kubelet   system:node:master        Approved,Issued
csr-gcrk7     46m    kubernetes.io/kube-apiserver-client-kubelet   system:bootstrap:96771a   Approved,Issued
```
8. Hmmm.. You are not aware of a request coming in. What groups is this CSR requesting access to?` `system:masters`
Check the details about the request. Preferebly in YAML.
```bash
master $ kubectl get csr agent-smith -o yaml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  creationTimestamp: "2020-09-25T02:55:52Z"
  managedFields:
  - apiVersion: certificates.k8s.io/v1beta1
    fieldsType: FieldsV1
    fieldsV1:
      f:spec:
        f:groups: {}
        f:request: {}
        f:signerName: {}
        f:usages: {}
    manager: kubectl
    operation: Update
    time: "2020-09-25T02:55:52Z"
  name: agent-smith
  resourceVersion: "7332"
  selfLink: /apis/certificates.k8s.io/v1beta1/certificatesigningrequests/agent-smith
  uid: 1336d527-e48b-4725-a6d3-2a446208f4ba
spec:
  groups:
  - system:masters
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1dEQ0NBVUFDQVFBd0V6RVJNQThHQTFVRUF3d0libVYzTFhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQgpBUVVBQTRJQkR3QXdnZ0VLQW9JQkFRRE8wV0pXK0RYc0FKU0lyanBObzV2UklCcGxuemcrNnhjOStVVndrS2kwCkxmQzI3dCsxZUVuT041TXVxOTlOZXZtTUVPbnJEVU8vdGh5VnFQMncyWE5JRFJYall5RjQwRmJtRCs1eld5Q0sKeTNCaWhoQjkzTUo3T3FsM1VUdlo4VEVMcXlhRGtuUmwvanYvU3hnWGtvazBBQlVUcFdNeDRCcFNpS2IwVSt0RQpJRjVueEF0dE1Wa0RQUTdOYmVaUkc0M2IrUVdsVkdSL3o2RFdPZkpuYmZlek90YUF5ZEdMVFpGQy93VHB6NTJrCkVjQ1hBd3FDaGpCTGt6MkJIUFI0Sjg5RDZYYjhrMzlwdTZqcHluZ1Y2dVAwdEliT3pwcU52MFkwcWRFWnB3bXcKajJxRUwraFpFV2trRno4MGxOTnR5VDVMeE1xRU5EQ25JZ3dDNEdaaVJHYnJBZ01CQUFHZ0FEQU5CZ2txaGtpRwo5dzBCQVFzRkFBT0NBUUVBUzlpUzZDMXV4VHVmNUJCWVNVN1FGUUhVemFsTnhBZFlzYU9SUlFOd0had0hxR2k0CmhPSzRhMnp5TnlpNDRPT2lqeWFENnRVVzhEU3hrcjhCTEs4S2czc3JSRXRKcWw1ckxaeTlMUlZyc0pnaEQ0Z1kKUDlOTCthRFJTeFJPVlNxQmFCMm5XZVlwTTVjSjVURjUzbGVzTlNOTUxRMisrUk1uakRRSjdqdVBFaWM4L2RoawpXcjJFVU02VWF3enlrcmRISW13VHYybWxNWTBSK0ROdFYxWWllKzBIOS9ZRWx0K0ZTR2poNUw1WVV2STFEcWl5CjRsM0UveTNxTDcxV2ZBY3VIM09zVnBVVW5RSVNNZFFzMHFXQ3NiRTU2Q0M1RGhQR1pJcFVibktVcEF3a2ErOEUKdndRMDdqRytocGtueG11RkFlWHhnVXdvZEFMYUo3anUvVERJY3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0K
  signerName: kubernetes.io/legacy-unknown
  usages:
  - digital signature
  - key encipherment
  - server auth
  username: agent-x
status: {}
```
9. That doesn't look very right. Reject that request.
```bash
master $ kubectl certificate deny agent-smith
certificatesigningrequest.certificates.k8s.io/agent-smith denied
master $ kubectl get  csr
NAME          AGE    SIGNERNAME                                    REQUESTOR                 CONDITION
agent-smith   5m9s   kubernetes.io/legacy-unknown                  agent-x                   Denied
akshay        27m    kubernetes.io/legacy-unknown                  kubernetes-admin          Approved,Issued
csr-b489f     49m    kubernetes.io/kube-apiserver-client-kubelet   system:node:master        Approved,Issued
csr-gcrk7     49m    kubernetes.io/kube-apiserver-client-kubelet   system:bootstrap:96771a   Approved,Issued
```
10. Let's get rid of it. Delete the new CSR object
```bash
master $ kubectl delete csr agent-smith
certificatesigningrequest.certificates.k8s.io "agent-smith" deleted
master $ kubectl get  csr
NAME        AGE   SIGNERNAME                                    REQUESTOR                 CONDITION
akshay      28m   kubernetes.io/legacy-unknown                  kubernetes-admin          Approved,Issued
csr-b489f   50m   kubernetes.io/kube-apiserver-client-kubelet   system:node:master        Approved,Issued
csr-gcrk7   50m   kubernetes.io/kube-apiserver-client-kubelet   system:bootstrap:96771a   Approved,Issued
```
