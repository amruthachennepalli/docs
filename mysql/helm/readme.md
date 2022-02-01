# MySql

https://artifacthub.io/packages/helm/bitnami/mysql

**Pre-Requisite :**

- Kubernetes
- Helm

**K3s Installation :**

```bash
curl -sfL https://get.k3s.io | sh -

# export Kubeconfig

export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
sudo chmod 644 /etc/rancher/k3s/k3s.yaml

k3s kubectl get node
```
**Helm Installation(binary/apt) :**

**Through Binary :**

```bash
# Download desired version 
wget https://get.helm.sh/helm-v3.7.2-linux-amd64.tar.gz

# Unpack it 
tar -zxvf helm-v3.7.2-linux-amd64.tar.gz

# Find the helm binary in the unpacked directory, and move it to its desired destination
sudo mv linux-amd64/helm /usr/local/bin/helm
```
**Through Apt package :**
```
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```
**MySql helm chart Installation :**

```bash
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm install my-release bitnami/mysql
```

**Decode for Server Username and Password**
```
  echo Username: root
  MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default my-release-mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode)
```

**Connecting to MySql through container**
```
kubectl run my-release-mysql-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mysql:8.0.27-debian-10-r35 --namespace default --command -- bash

mysql -h my-release-mysql.default.svc.cluster.local -uroot -p"$MYSQL_ROOT_PASSWORD"
```


**Uninstall Chart**

```bash
helm delete my-release
````
