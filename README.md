# confluent-kubernetes
Confluent kafka cluster in Kubernetes
```
helm repo add confluentinc https://packages.confluent.io/helm
helm repo update
helm upgrade --install confluent-operator confluentinc/confluent-for-kubernetes --namespace confluent --create-namespace
kubectl get pods -n confluent

kubectl get crds | grep confluent
```

<img width="977" height="571" alt="image" src="https://github.com/user-attachments/assets/f09898f4-c6d4-49ec-ad4d-6f02854d0db9" />

```
cat kafka.ext

authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = *.kafka.confluent.svc.cluster.local
DNS.2 = *.kraftcontroller.confluent.svc.cluster.local
DNS.3 = *.confluent.svc.cluster.local
DNS.4 = localhost
```
```
openssl genrsa -out ca.key 4096
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 -subj "/CN=Confluent-Internal-CA" -out ca.crt
openssl genrsa -out tls.key 2048
openssl req -new -key tls.key -out kafka.csr -subj "/CN=kafka.confluent.svc.cluster.local"
openssl x509 -req -in kafka.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out tls.crt -days 3650 -sha256 -extfile kafka.ext
kubectl create secret generic tls-kafka --from-file=tls.crt=tls.crt --from-file=tls.key=tls.key --from-file=ca.crt=ca.crt --namespace confluent
```
<img width="1906" height="972" alt="image" src="https://github.com/user-attachments/assets/7ffd1a96-7715-485e-8d97-c2889dc4b356" />
<img width="1902" height="971" alt="image" src="https://github.com/user-attachments/assets/c834edc6-41b2-4cad-889a-c72f1b6b962c" />
<img width="1892" height="965" alt="image" src="https://github.com/user-attachments/assets/be978bea-9e7c-42a2-bd50-9e962276e250" />
<img width="886" height="377" alt="image" src="https://github.com/user-attachments/assets/7f034b9b-8d15-4651-8cb3-c28b1aaf3842" />
<img width="1825" height="580" alt="image" src="https://github.com/user-attachments/assets/9fc2c25f-1971-4f89-aa73-7cedf7f99210" />

