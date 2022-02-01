# Deploy Nginx application with deployment and service files in K8s using flux

**Pre-requisites :**

- Kubernetes (K3s/K3d/Klusternetes)
- flux-cli
- nginx source file
- nginx deployment file


K3s installation :

```bash
curl -sfL https://get.k3s.io | sh -

#Export Kubeconfig file in local
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
sudo chmod 644 /etc/rancher/k3s/k3s.yaml
```

Install flux-cli :

```bash
curl -s https://fluxcd.io/install.sh | sudo bash

#Check flux in cluster :
flux check --pre
```
**The output is similar to:**
```
► checking prerequisites
✔ kubernetes 1.22.2 >=1.19.0
✔ prerequisites checks passed
```
Export your credentials :

```
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<user-name>
export GITHUB_REPO= <repo-name>
```
Install Flux onto your cluster :
```
flux bootstrap github \
    --owner=${GITHUB_USER} \
    --repository=${GITHUB_REPO} \
    --branch=main \
    --read-write-key=true \
    --private=false \
    --path=<dir-path>
    --personal
```
**The output is similar to :**
```
► connecting to github.com
✔ repository created
✔ repository cloned
✚ generating manifests
✔ components manifests pushed
► installing components in flux-system namespace
deployment "source-controller" successfully rolled out
deployment "kustomize-controller" successfully rolled out
deployment "helm-controller" successfully rolled out
deployment "notification-controller" successfully rolled out
✔ install completed
► configuring deploy key
✔ deploy key configured
► generating sync manifests
✔ sync manifests pushed
► applying sync manifests
◎ waiting for cluster sync
✔ bootstrap finished
```
Add nginx repository to Flux :
```
flux create source git nginx \
  --url=https://github.com/${GITHUB_USER}/${GITHUB_REPO} \
  --branch=main \
  --interval=30s \
  --export > ./clusters/my-cluster/nginx-source.yaml
```
Deploy nginx application :
```
  flux create kustomization nginx \
  --target-namespace=default \
  --source=podinfo \
  --path=./clusters/my-cluster \
  --prune=true \
  --interval=5m \
  --export > ./clusters/my-cluster/nginx-kustomization.yaml
```  

Commit and push the Kustomization manifest to the repository:

```
git add -A && git commit -m "Add podinfo Kustomization"
git push
```

The structure of the fleet-infra repo should be similar to:

```
fleet-infra
└── clusters/
    └── my-cluster/
        ├── flux-system/                        
        │   ├── gotk-components.yaml
        │   ├── gotk-sync.yaml
        │   └── kustomization.yaml
        ├── nginx-kustomization.yaml
        └── nginx-source.yaml
        └── deployment.yaml
        └── service.yaml
```        

