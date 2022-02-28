# SOFTONICS DEV SETUP

## Install Kind:
```bash
sudo curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
sudo chmod +x ./kind
kind create cluster –name=sft-dev  (creates a cluster)
```

## Install Skaffold:
```bash
sudo curl -o /usr/local/bin/skaffold -L https://storage.googleapis.com/skaffold/releases/v1.26.1/skaffold-linux-amd64
sudo chmod +x /usr/local/bin/skaffold
```

## Install helm:
```bash
wget https://get.helm.sh/helm-v3.7.2-linux-amd64.tar.gz 2.tar -zxvf helm-v3.7.2-linux-amd64.tar.gz 
sudo mv linux-amd64/helm /usr/local/bin/helm
```

## Install ctlptl:
```bash
curl -fsSL https://github.com/tilt-dev/ctlptl/releases/download/v0.7.5 /ctlptl.0.7.5.linux.x86_64.tar.gz | sudo tar -xzv -C /usr/local/bin ctlptl
sudo chmod +x ctlptl
```

## Install helm secrets plugin:
```bash
helm plugin install https://github.com/futuresimple/helm-secrets
```

## Install sops:
```
curl -o /usr/local/bin/sops -L https://github.com/mozilla/sops/releases/download/v3.7.1/sops-v3.7.1.linux2. chmod +x /usr/local/bin/sops
```

Task STEPS: 
```bash
# clone the  bit-bucket repo (affiliate-api) with make file
mkdir softonic
git clone 
cd softonic/affiliate-api 
# export Hostpath
export KIND_HOSTPATH=/home/swathi/softonic/affiliate-api

# Add helm repos
helm repo add softonic https://admin:TxlEVEssXbvE@chartmuseum.softonic.io/
helm repo add softonic-public https://admin:LQ2735wD3GjS47Xm@charts.softonic.io

# create dev env with makefile
make dev

# login to docker registry
docker login  registry.softonic.io 

{"registry.softonic.io":
{"username":"softonic","password":"D3LLiSF4nD4ng0","auth":"c29mdG9uaWM6R
DNMTGlTRjRuRDRuZzA="}}
```
