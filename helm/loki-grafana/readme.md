# Loki

https://artifacthub.io/packages/helm/grafana/loki

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

## Loki Installations :

```bash
# Get Repo Info
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# Deploy Loki only in custome namespace "loki"
kubectl create namespace loki

# Deploy Loki Stack (Loki, Promtail, Grafana, Prometheus) with persistent volume claim
helm upgrade --install loki grafana/loki-stack -n loki --set grafana.enabled=true,prometheus.enabled=true,prometheus.alertmanager.persistentVolume.enabled=false,prometheus.server.persistentVolume.enabled=false,loki.persistence.enabled=true,loki.persistence.storageClassName=standard,loki.persistence.size=5Gi
```
**Deploy Grafana to your cluster**

```bash
helm install loki-grafana grafana/grafana

# To get the admin password for the Grafana pod, run the following command
kubectl get secret -n loki loki-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

To access the Grafana UI, run the following command:

**Note : Make sure all pods are in RUNNING status**

```
kubectl port-forward -n loki service/loki-grafana 3000:80
```
Navigate to http://localhost:3000 and login with admin and the password output above. Then follow the instructions for adding the Loki Data Source, using the URL http://loki:3100/ for Loki.


![Screenshot from 2021-12-22 12-09-57](https://user-images.githubusercontent.com/82360490/147047487-4de770a1-64f8-47e4-bf2f-d5f104ed9f16.png)


