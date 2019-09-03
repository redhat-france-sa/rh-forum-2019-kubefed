```
oc --context=cluster01 create ns pacman
kubefedctl federate namespace pacman --kubefed-namespace kubefed-system --host-cluster-context=cluster01
oc --context=cluster01 create -n pacman  -f yaml-resources/01-mongo-federated-secret.yaml
oc --context=cluster01 create -n pacman  -f yaml-resources/02-pacman-federated-service.yaml
oc --context=cluster01 create -n pacman  -f yaml-resources/03-pacman-federated-ingress.yaml
```
