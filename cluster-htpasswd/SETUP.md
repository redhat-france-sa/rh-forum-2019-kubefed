# Add htpasswd identity provider to OCP

## Step 1, manual installation

### Install htpasswd tool

```
sudo yum install httpd-tools
```

### Generate htpasswd file

```
htpasswd -c -B -b ./ocp-htpasswd <user> <password>
```

Continue with same command by adding as many credentials as required.


### Create OCP HTPasswd Secret

```
oc create secret generic htpass-secret --from-file=htpasswd=./ocp-htpasswd -n openshift-config
```

Take care of the key name (htpasswd) included in the from-file value.

### Create Custom Resource (CR) for identity providers.

```
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: cluster_htpasswd_provider 
    mappingMethod: claim 
    type: HTPasswd
    htpasswd:
      fileData:
        name: htpass-secret 
```

Then apply this CR :

```
oc apply -f - <<EOF
---
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: cluster_htpasswd_provider
    mappingMethod: claim
    type: HTPasswd
    htpasswd:
      fileData:
        name: htpass-secret
---
EOF
```

### Add cluster-admin role to new super admin

```
oc adm policy add-cluster-role-to-user cluster-admin xymox
```

Then remove kubeadmin default user:

```
oc login -u xymox
oc delete secrets kubeadmin -n kube-system
```

## Or via Ansible automation

```
ansible-playbook -vvv -i localhost, --connection=local -e '{ "kube": { "config": { "path": "/home/vagrant/fed-ocp-demo/cluster03/auth/kubeconfig" } } }' htpasswd-identity.yaml
```
