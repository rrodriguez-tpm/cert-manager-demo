# Demo Manifests And Application Used In DevOps Toolkit Videos

* [What Is HTTPS? How Does It Work? Automate With cert-manager And Let's Encrypt](https://youtu.be/D7ijCjE31GA)


## k3s on Ubuntu

### Installation
```
curl -sfL https://get.k3s.io | sh -
```

### To issue command to k3s without sudo. It is not recommended for production environments.
```
sudo chmod 777 /etc/rancher/k3s/k3s.yaml
```

### Check the newly deployed nodes:
```
kubectl get nodes
```

### To prepare the cluster:
```
kubectl config view --raw > ~/.kube/config
```

## Create a Kubernetes cluster with Ingress set as default:
```
helm repo add jetstack https://charts.jetstack.io

helm repo update

helm upgrade --install cert-manager jetstack/cert-manager \
    --namespace cert-manager --create-namespace \
    --set installCRDs=true --wait

kubectl create namespace production
```

## Set up DNS records to point to the `EXTERNAL-IP` of the Ingress Service

## Application Without HTTPS Access
```
cd cert-manager-demo

cat app/ing.yaml

kubectl --namespace production apply --filename app

curl http://pharmacycenter.io
```

## Enable HTTPS Access Through cert-manager And Let's Encrypt
```
cat issuer.yaml

kubectl apply --filename issuer.yaml

cat certificate.yaml

kubectl --namespace production apply --filename certificate.yaml
```

### Add `cert-manager.io/cluster-issuer: production` to `metadata.labels` in `app/ing.yaml`

## Apply the changes:
```
kubectl --namespace production apply --filename app

kubectl --namespace production \
    get issuers,certificaterequests,certificates,orders,secrets

curl http://pharmacycenter.io
```

## When the application has been modified and needs to be deployed:
```
kubectl -n production delete -f app
kubectl -n production apply -f app
```

## Uninstall k3s from a server node
```
/usr/local/bin/k3s-uninstall.sh
```