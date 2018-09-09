# install cluster

# connect to cluster
```
gcloud container clusters get-credentials main --zone europe-west1-b --project <project-id>
```

# install helm
```
kubectl apply -f helm-rbac.yaml
helm init --service-account tiller --upgrade
kubectl -n kube-system get pods -l name=tiller
```

# Ingress
```
helm install --name nginx-ingress stable/nginx-ingress --set rbac.create=true --namespace ingress
```

# Cert manager
```
helm install --name cert-manager \
    --namespace ingress \
    --set ingressShim.defaultIssuerName=letsencrypt-prod \
    --set ingressShim.defaultIssuerKind=ClusterIssuer \
    stable/cert-manager
```

# Get IP and change DNS
```
kubectl get svc -l app=nginx-ingress,component=controller -o=jsonpath='{$.items[*].status.loadBalancer.ingress[].ip}'
```

# Certificate Issuer
```
kubectl apply -f issuer.yaml
```

## Per app
```
cd echo-example
kubectl apply -f ingress.yaml
kubectl apply -f echoserver.yaml
```

## Addendum: autoscaling
```
gcloud container clusters update main --enable-autoscaling --min-nodes 1 --max-nodes 2
```
