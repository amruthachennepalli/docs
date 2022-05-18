# Rancher-fleet

 Fleet is a low-level cluster engine that feels like a distributed init system. With fleet, you can treat your CoreOS cluster as if it shared a single init system.


* https://rancher.com/docs/rancher/v2.5/en/installation/install-rancher-on-k8s/
* https://rancher.com/docs/rancher/v2.5/en/deploy-across-clusters/fleet/
* https://github.com/rancher/fleet
* https://github.com/rancher/fleet-examples
* https://rancher.com/docs/rancher/v2.5/en/cluster-provisioning/registered-clusters/#configuring-a-k3s-cluster-to-enable-registration-in-rancher

## Pre-Requisite :

- klusternetes cluster -large
- helm

## Helm Installation(binary/apt) : 

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
**Install rancher using Docker :**

```bash
sudo docker run --privileged -d --restart=unless-stopped -p 80:80 -p 443:443 rancher/rancher
```

**Install rancher with helm :**

```bash
#Add helm Repo
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest

#Create custom namespace
kubectl create namespace cattle-system

#Install rancher in custome namespace
helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set hostname=rancherfleet.5gwiyaephssbhc6fvqdc.apps.klusternetes.com\
  --set replicas=3 \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=amrutha@zelarsoft.com
```  
  
**Note : Make sure all pods are in running state**   

**Verify the Rancher Server Deployments:**

```bash
kubectl -n cattle-system get deploy rancher

kubectl -n cattle-system rollout status deploy/rancher 
``` 
**Then browse https://rancherfleet.5gwiyaephssbhc6fvqdc.apps.klusternetes.com for Rancher UI**

## Note : Fleet comes preinstalled in Rancher v2.5. if not try below command to install fleet

**Install fleet with helm :**

```
VERSION=0.3.5
helm -n fleet-system install --create-namespace --wait \
    fleet-crd https://github.com/rancher/fleet/releases/download/v${VERSION}/fleet-crd-${VERSION}.tgz
helm -n fleet-system install --create-namespace --wait \
    fleet https://github.com/rancher/fleet/releases/download/v${VERSION}/fleet-${VERSION}.tgz    
```

**Add GitRepo to rancher :**

```bash
cat > example.yaml << "EOF"
apiVersion: fleet.cattle.io/v1alpha1
kind: GitRepo
metadata:
  name: sample
  # This namespace is special and auto-wired to deploy to the local cluster
  namespace: fleet-local
spec:
  branch: master
  paths: 
  - "simple"
  # Everything from this repo will be ran in this cluster. You trust me right?
  repo: "https://github.com/rancher/fleet-examples.git"
EOF

kubectl apply -f example.yaml
```

