# Thanos

https://artifacthub.io/packages/helm/bitnami/thanos

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
##  Helm Installation(binary/apt) :  

**Binary :**

```bash
# Download desired version 
wget https://get.helm.sh/helm-v3.7.2-linux-amd64.tar.gz

# Unpack it 
tar -zxvf helm-v3.7.2-linux-amd64.tar.gz

# Find the helm binary in the unpacked directory, and move it to its desired destination
sudo mv linux-amd64/helm /usr/local/bin/helm
```
**Apt package :**
```
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

**Thanos helm chart Installation :**

```bash
# Get Repo Info
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Install Chart
helm install my-release bitnami/thanos
```
You can access Thanos using port forwarding:

```
kubectl port-forward svc/my-release-thanos-query-frontend 9090
```

Then browse https://localhost:9090 for Thanos UI


![Screenshot from 2021-12-22 12-34-18](https://user-images.githubusercontent.com/82360490/147050576-a8946b2c-634f-4b97-af2b-36745873f668.png)



