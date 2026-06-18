# confluent-kubernetes
Confluent kafka cluster in Kubernetes
```
helm repo add confluentinc https://packages.confluent.io/helm
helm repo update
helm upgrade --install confluent-operator confluentinc/confluent-for-kubernetes --namespace confluent --create-namespace
kubectl get pods -n confluent

kubectl get crds | grep confluent
```
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
