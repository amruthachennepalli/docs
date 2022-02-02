# Self-Hosted Gitpod

## Prerequisites :
- Instance Type :T32Xlarge
- Kubernetes 1.13+
- Helm 3+
- Rabbitmq (database)


**K3s Insallation :**
```bash
 curl -sfL https://get.k3s.io | sh -
 
 export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
 sudo chmod 644 /etc/rancher/k3s/k3s.yaml
 
# check for kubectl  
 kubectl version
```

**Helm Instalation :**

```bash
# download helm
wget https://get.helm.sh/helm-v3.7.0-linux-amd64.tar.gz

#uzip helm zip-file
tar -zxvf helm-v3.7.0-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm

#check for helm
helm version
```
**Rabbitmq Installation :**

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install rabbitmq bitnami/rabbitmq

# run decoding command for rabbitmq password and update with values.custom.yaml (ex: UQuUy4vIOo)
```

**Gitpod Installation :**
```bash
# Create a file values.custom.yaml and update with respective below details.

docker-registry:
  authentication:
    username: gitpod
    password: your-registry-password
rabbitmq:
  auth:
    username: your-rabbitmq-user
    password: your-secret-rabbitmq-password
minio:
  accessKey: your-random-access-key
  secretKey: your-random-secret-key
```
```bash
# Add gitpod helm repo
helm repo add gitpod.io https://charts.gitpod.io
helm repo update

# install gitpod by using values.yaml
helm install -f values.custom.yaml gitpod gitpod.io/gitpod --version=0.10.0

#create DNS A reacords with pub.IP (gitpod.domine & *.gitpod.domine),and check for dns update
dig +short gitpod.zsdevops.online   

kubectl get pod
# To list helm charts
helm list -A

#To list helm repos
helm repo list -A

#To delete helm chart
helm delete traefik-crd -n kube-system
helm delete traefik -n kube-system

#To stop k3s cluster
systemctl stop k3s
```
**Install socat and certbot :**
```bash
#Install socat
apt update && apt install socat

wget -O -  https://get.acme.sh | sh
/root/.acme.sh/acme.sh --issue -d gitpod.zsdevops.online   --yes-I-know-dns-manual-mode-enough-go-ahead-please  --standalone --register-account -m amrutha@zelarsoft.com
/root/.acme.sh/acme.sh --issue -d gitpod.zsdevops.online   --yes-I-know-dns-manual-mode-enough-go-ahead-please  --standalone
/root/.acme.sh/acme.sh --issue -d gitpod.zsdevops.online  --yes-I-know-dns-manual-mode-enough-go-ahead-please  --standalone

#Install certbot
apt install certbot 

certbot certonly --manual --preferred-challenges=dns --email amrutha@zelarsoft.com --server https://acme-v02.api.letsencrypt.org/directory --agree-tos -d *.gitpod.sree-cloud-devops.online  -d *.ws.gitpod.sree-cloud-devops.online   

#create DNS TXT record with result of above command of TXT key & value of wildcard domains one by one confirm before hit ENTER
cd /etc/letsencrypt/archive/gitpod.zsdevops.online/

#create secrete for certs
kubectl create secret generic https-certificates --from-file=.
kubectl get secret https-certificates -oyaml
mkdir certs
cp *.pem certs/ && cd certs
mv cert1.pem cert.pem && mv chain1.pem chain.pem && mv fullchain1.pem fullchain.pem && mv privkey1.pem privkey.pem
openssl dhparam -out certs/dhparams.pem 2048

kubectl create secret generic https-certificates --from-file=.
cp fullchain.pem tls.crt && cp privkey.pem tls.key

kubectl delete secret https-certificates
kubectl create secret generic https-certificates --from-file=.**

#Install gitpod with updated values.yaml
helm upgrade --install -f values.yaml gitpod gitpod.io/gitpod --version=0.10.0
kubectl get pods

#To check database helm chart version run below command
helm show chart bitnami/mysql  
```
   



