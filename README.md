https://gist.github.com/user9384732902/fe340addc24c7a45de7ede214e829cfa

# install cluster

# connect to cluster
```
gcloud container clusters get-credentials main --zone europe-west1-b --project platyplus-0402
```

# install helm
```
kubectl apply -f helm-rbac.yaml
helm init --service-account tiller --upgrade
kubectl -n kube-system get pods -l name=tiller
```

# Cert manager
```
helm install --name cert-manager stable/cert-manager --set ingressShim.extraArgs='{--default-issuer-name=letsencrypt-prod,--default-issuer-kind=ClusterIssuer}' --set ingressShim.enabled=false --namespace kube-system
```

# Ingress
```
helm install --name nginx-ingress stable/nginx-ingress --set rbac.create=true
```

# Get IP and change DNS
```
kubectl get svc -l app=nginx-ingress,component=controller -o=jsonpath='{$.items[*].status.loadBalancer.ingress[].ip}'
```

# Issuer
```
kubectl apply -f issuer.yaml
```

## Per app
```
cd echo-example
kubectl apply -f certificate.yaml
kubectl apply -f ingress.yaml
kubectl apply -f echoserver.yaml
```

## Addendum: autoscaling
```
gcloud container clusters update main --enable-autoscaling --min-nodes 1 --max-nodes 2
```
