# Deploy Nginx on K8S with ArgoCD

## pre-req :

- kubernetes
- helm
- argocd CLI

**Install argocd :**
```bash
#create NameSpace 
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
**Access The Argo CD API Server**

## Note: Here you can access argocd server by Service Type Load Balancer,Ingress and Port Forwarding.


**Service Type Load Balancer**
```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
**Ingress**
```bash
# follow this doc link https://argo-cd.readthedocs.io/en/release-1.8/operator-manual/ingress/

cat > ingress.yaml << "EOF"
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: argocd-server-ingress
  namespace: argocd
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
spec:
  rules:
  - host: argocd.example.com
    http:
      paths:
      - backend:
          serviceName: argocd-server
          servicePort: https
          
EOF

kubectl apply -f ingress.yaml          
```

**Port Forwarding**
```bash
# To get argocd password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

kubectl port-forward svc/argocd-server -n argocd 8080:443
```
### Install Argocd CLI

https://argo-cd.readthedocs.io/en/stable/cli_installation/

```bash
# Installing argocd cli on Ubuntu
wget https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64 -O argocd 

chmod +x argocd 

sudo mv argocd /usr/local/bin  

argocd --help 
```
**Login to Argocd trough cli**
```bash
argocd login <argocd server>

argocd account update-password
```

**Add multiple cluster to argocd**

```bash
#To get cluster name
kubectl config get-contexts -o name

# adding cluster with sample c2 named cluster
argocd cluster add Default --name=c2

# list all cluster
argocd cluster list
```

**Add applications to Argocd**

```bash
argocd app create nginx --repo https://github.com/zs-amrutha/fleet-examples.git --path nginx --dest-server https://kubernetes.default.svc --dest-namespace default

argocd app sync nginx

#To list apps
argocd app list
```

**Add and deploy application using ApplicationSet**

```bash
# Install argocd ApplicationSet controller
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj-labs/applicationset/v0.2.0/manifests/install.yaml

#guestbook app deployment file
cat > appset.yaml << "EOF"
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: guestbook
spec:
  generators:
  - list:
      elements:
      - cluster: c2
        url: https://vc-z8vsu8clunqwd8xnoqkf.apps.aws-mumbai-eks.klusternetes.com
  template:
    metadata:
      name: '{{cluster}}-guestbook'
    spec:
      project: default
      source:
        repoURL: https://github.com/zs-amrutha/fleet-examples.git
        targetRevision: HEAD
        path: simple/{{cluster}}
      destination:
        server: '{{url}}'
        namespace: guestbook
EOF

kubectl apply -f appset.yaml -n argocd
```

**Add application with helm charts**
```bash
argocd app create monitoring \
  --repo https://kubernetes-charts.storage.googleapis.com \
  --helm-chart prometheus \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --revision 9.7.4

argocd app list           # To view all applications
argocd app get monitoring # To view details about the monitoring Application we just created.

#delete app
argocd app delete <appname>
```     
        
        
        
