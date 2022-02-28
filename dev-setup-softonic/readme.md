# SOFTONICS DEV SETUP

## pre-req

* Install kind
* Install skaffold
* Install helm
* Install ctlptl
* Install helm secrets plugin
* Install sops

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

### TASK STEPS: 
```bash
# clone the  bit-bucket repo (affiliate-api) with make file
mkdir softonic
git clone 
cd softonic/affiliate-api 

# export Hostpath
export KIND_HOSTPATH=/home/amrutha/softonic/affiliate-api

# Add helm repos
helm repo add softonic https://admin:TxlEVEssXbvE@chartmuseum.softonic.io/
helm repo add softonic-public https://admin:LQ2735wD3GjS47Xm@charts.softonic.io

# login to docker registry
docker login  <registry>

# create dev env with makefile
make dev
```

## Generate the token

```sh
SECRET=$(kubectl get sa argo-server -o=jsonpath='{.secrets[0].name}')
ARGO_TOKEN="Bearer $(kubectl get secret $SECRET -o=jsonpath='{.data.token}' | base64 --decode)"
echo $ARGO_TOKEN
```
## Execute these export commands

```sh
export ARGO_SERVER=localhost:2746
export ARGO_SECURE=false
export ARGO_INSECURE_SKIP_VERIFY=true
export ARGO_SECURE=false
export ARGO_TOKEN=$ARGO_TOKEN
```

## Access argo server UI via web

```sh
container=$(k get pods -l app.kubernetes.io/component=server -o name)
kubectl port-forward $container 2746:2746 &
```

**You can now access argo UI via browser**

http://localhost:2746

## Adapt the test submit file


**You can modify the after and the before with your data.
Also set the proper repository and the other params**

```sh
argo submit --serviceaccount  argo-workflow --from wftmpl/repo-pipeline\
 --entrypoint=start\
 -p before=deb21146832657a752777d59254cfda6306ce400\
 -p after=f346c63679160510081dec184cbcefe202513e40\
 -p branch=release/SOAL-123-test-volatile\
 -p repository=git@github.com:softonic/test-deployment-helm.git\
 -p repository-name=test-deployment-helm\
 -p manifest-namespace=argo\
 -p secret-key-name=github-key\
 -p pr-id=6.46809615e+08\
 -p git-provider=github\
 -p workflow-name=test-deployment-helm-pullrequest-feat--test-volatile-cdvff\
 -p branch-prefix=release\
 -p task-id=SOAL-123
``` 


