---
title: "Identity Providers Tips by Example"
linkTitle: "Identity Providers"
description: "OpenShift CLI identity providers commands by example with tips and tricks from the experts"
date: 2019-06-12T16:42:20+02:00
lastmod: 2019-06-18T16:42:20+02:00
publishdate: 2019-06-18T16:42:20+02:00
author: Red Hat Communities of Practice ([@RedHatCoP](https://twitter.com/RedHatCoP)), OpenShift.Tips ([Team](https://openshift.tips/about/))
draft: false
weight: 20
---

## Add HTPasswd authentication (OpenShift 4 only)

### Create htpasswd file (with admin username)

```sh
htpasswd -c htpasswd admin
```

### Create secret in openshift-config project

```sh
oc create secret generic htpasswd-secret --from-file htpasswd -n openshift-config
```

### Edit cluster OAuth resource

```sh
cat << EOF | oc apply -f -
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: htpasswd
    challenge: true
    login: true
    mappingMethod: claim
    type: HTPasswd
    htpasswd:
      fileData:
        name: htpasswd-secret
EOF
```

### Optional: grant cluster-admin role

```sh
oc adm policy add-cluster-role-to-user cluster-admin admin
```

## Remove kubeadmin user

```sh
oc delete secret kubeadmin -n kube-system
```
