```
oc create namespace kubefed-mongo
kubefedctl federate namespace kubefed-mongo --contents --enable-type kubefed-mongo --kubefed-namespace kubefed-system --host-cluster-context=cluster01

oc --context=cluster01 create -n kubefed-mongo -f kubefed-configs/01-mongo-federated-secret.yaml
oc --context=cluster01 create -n kubefed-mongo -f kubefed-configs/02-mongo-federated-service.yaml
oc --context=cluster01 create -n kubefed-mongo -f kubefed-configs/03-mongo-federated-pvc.yaml
oc --context=cluster01 create -n kubefed-mongo -f kubefed-configs/04-mongo-federated-deployment-rs.yaml

oc --context=cluster01 -n kubefed-mongo create route passthrough mongo --service=mongo --port=27017 --hostname=mongo-cluster01.apps.cluster01.clustership.com
oc --context=cluster02 -n kubefed-mongo create route passthrough mongo --service=mongo --port=27017 --hostname=mongo-cluster02.apps.cluster02.clustership.com
oc --context=cluster03 -n kubefed-mongo create route passthrough mongo --service=mongo --port=27017 --hostname=mongo-cluster03.apps.cluster03.clustership.com

oc --context=cluster01 -n kubefed-mongo get deployment  mongo
oc --context=cluster02 -n kubefed-mongo get deployment  mongo
oc --context=cluster03 -n kubefed-mongo get deployment  mongo

#
# Within 5 minutes
#
MONGO_POD=$(oc --context=cluster01 -n kubefed-mongo get pod --selector="name=mongo" --output=jsonpath='{.items..metadata.name}')
oc --context=cluster01 -n kubefed-mongo label pod $MONGO_POD replicaset=primary

oc --context=cluster01 -n kubefed-mongo exec $MONGO_POD -- bash -c 'mongo --norc --quiet --username=admin --password=clyde --host localhost admin --tls --tlsCAFile /opt/mongo-ssl/ca.pem --eval "rs.status()"'
oc --context=cluster01 -n kubefed-mongo exec $MONGO_POD -- bash -c 'mongo --norc --quiet --username=admin --password=clyde --host mongo-cluster03.apps.cluster03.clustership.com --port 443 admin --tls --tlsCAFile /opt/mongo-ssl/ca.pem --eval "rs.status()"'
```
