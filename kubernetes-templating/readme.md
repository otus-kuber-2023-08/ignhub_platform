#### helm-v3 install
download https://github.com/helm/helm/releases
tar -zxvf helm-v3.X.X-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
helm repo add stable https://charts.helm.sh/stable

#### ingress-nginx
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
kubectl create ns ingress-nginx
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx --wait --namespace=ingress-nginx

#### cert-manager
helm repo add jetstack https://charts.jetstack.io
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --set installCRDs=true 
# Or kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.3/cert-manager.crds.yaml
#--version v1.13.3 \

In order to begin issuing certificates, you will need to set up a ClusterIssuer
or Issuer resource (for example, by creating a 'letsencrypt-staging' issuer).
https://cert-manager.io/docs/configuration/
configure cert-manager to automatically provision Certificates for Ingress resources, see ingress-shim
https://cert-manager.io/docs/usage/ingress/ 

k apply -f cert-manager/letsencrypt-stage-issuer.yaml
k apply -f cert-manager/letsencrypt-prod-issuer.yaml

#### chartmuseum
get LB_EXTERNAL_IP: 
  k get svc -n ingress-nginx
change LB_EXTERNAL_IP in chartmuseum/values.yaml

helm repo add chartmuseum https://chartmuseum.github.io/charts
add chartmuseum/values.yaml
helm upgrade --install chartmuseum chartmuseum/chartmuseum --wait \
 --namespace=chartmuseum \
 --create-namespace \
 -f chartmuseum/values.yaml

check https://chartmuseum.<LB_EXTERNAL_IP>.traefik.me

#### chartmuseum *
Опишите в PR последовательность действий, необходимых для
добавления туда helm chart's и их установки с использованием
chartmuseum как репозитория.

helm repo add local https://chartmuseum.<LB_EXTERNAL_IP>.traefik.me
helm search repo local
helm install local/mychart --generate-name

#### harbor (self-study task)
helm repo add harbor https://helm.goharbor.io
add chartmuseum/values.yaml use same LB_EXTERNAL_IP

helm upgrade --install harbor harbor/harbor --wait \
 --namespace=harbor \
 --create-namespace \
 -f harbor/values.yaml
 
#### helmfile *
-

#### make helm chart
Create helm structure: helm create hipster-shop 
cp all-hipster-shop.yaml hipster-shop/templates
rm values.yaml
kubectl create ns hipster-shop
helm upgrade --install hipster-shop hipster-shop --namespace hipster-shop

check UI:
k get svc -n hipster-shop
get frontend NodePort <NodePort_Frontend>
go to http://<k8s_Node_External_IP>:<NodePort_Frontend>

Move frontend to another chart
helm upgrade --install frontend frontend --namespace hipstershop
Make Ingress and check site availability

Make values and dependencies.
helm upgrade --install hipster-shop hipster-shop --namespace hipster-shop
Change params in dependencies:
helm upgrade --install hipster-shop hipster-shop --namespace hipster-shop --set frontend.service.NodePort=31234
Check site availability

#### make helm chart requirements.yaml *
-

#### helm-secrets *
-

#### jsonnet - kubecfg (additional task)
-

#### kubecfg *
-

#### kustomize (additional task)
-