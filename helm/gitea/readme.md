# Gitea

https://artifacthub.io/packages/helm/k8s-land/gitea

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
**gitea helm chart Installation :**

```bash
helm repo add k8s-land https://charts.k8s.land
helm install gitea k8s-land/gitea
```

**port-forwording**

```bash
kubectl port-forward svc/gitea-gitea-http 3000
```

Now open in localhost using http://localhost:3000 you will get UI
![image](https://user-images.githubusercontent.com/82368153/146919158-4d9621ee-d6ea-4daf-bab3-8fb57ab393ae.png)

**Uninstall Chart**

```bash
helm delete gitea
