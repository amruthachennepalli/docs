# Joomla

https://artifacthub.io/packages/helm/bitnami/joomla

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
**joomla helm chart Installation :**

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-release bitnami/joomla
```

**port-forwording**

```bash
kubectl port-forward svc/my-release-joomla 3000:80
```

Now open in localhost using http://localhost:3000 you will get UI

![image](https://user-images.githubusercontent.com/82368153/146913158-9d1ffc3a-a426-4ccf-8600-2d9fb89d89f7.png)

**login credentials**

username : user

password : 
```bash
echo Password: $(kubectl get secret --namespace default my-release-joomla -o jsonpath="{.data.joomla-password}" | base64 --decode)
```

**Uninstall Chart**

```bash
helm delete my-release
```





