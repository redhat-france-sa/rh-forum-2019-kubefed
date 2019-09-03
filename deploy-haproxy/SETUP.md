```
oc create -n federated-pacman configmap haproxy --from-file=haproxy

oc create -n federated-pacman -f haproxy-service.yaml
# Create the HAProxy deployment
oc create -n federated-pacman -f haproxy-deployment.yaml
```

