# Minio

https://artifacthub.io/packages/helm/bitnami/minio

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
**Helm Installation(binary/apt) : **

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

**minio helm chart Installation :**

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-release bitnami/minio

# Decode for Server user and Password 

kubectl get secret --namespace default my-release-minio -o jsonpath="{.data.root-user}" | base64 --decode
kubectl get secret --namespace default my-release-minio -o jsonpath="{.data.root-password}" | base64 --decode
```
   **OR**

Specify each parameter using the --set key=value[,key=value] argument to helm install. For example

```
helm install my-release \
  --set auth.rootUser=minio-admin \
  --set auth.rootPassword=minio-secret-password \
    bitnami/minio
```
You can access Minio using port forwarding:

```
kubectl port-forward --namespace default svc/my-release-minio 9001:9001
```
Then browse https://localhost:9001 for Minio UI and login with Decoded or --set Key & Values.

## Minio Dashboard

![Screenshot from 2021-12-21 15-26-23](https://user-images.githubusercontent.com/82360490/146911863-d714618b-9d7f-4810-a262-cf33e0bbf269.png)


**Uninstall Chart :**

```
helm delete my-release
```
