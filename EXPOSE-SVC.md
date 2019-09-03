```
oc create service clusterip hello-openshift --tcp=8080:8080
oc expose svc hello-openshift
```

