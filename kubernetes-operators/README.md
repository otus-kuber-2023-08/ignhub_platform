### install kopf
sudo apt install python3-pip
pip install kopf
pip install kubernetes
pip install jinja2
cd kubernetes-operators/build
kopf run mysql-operator.py

### show resources
k get crd
k get ms
k describe ms mysql-instance

### HINT remove stuck CRD deletion
https://rogulski.it/blog/kubernetes-stuck-resource-action/
add to cr.yml empty finalizers and apply
metadata:
  finalizers: []
  
### HINT make pv work for pvc in minikube
set in PersistentVolume
spec:
  storageClassName: standard

