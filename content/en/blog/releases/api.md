---
title: "API Tips by Example"
linkTitle: "API"
description: "OpenShift CLI API commands by example with tips and tricks from the experts"
date: 2019-06-18T16:42:20+02:00
lastmod: 2019-06-18T16:42:20+02:00
publishdate: 2019-06-18T16:42:20+02:00
author: Red Hat Communities of Practice ([@RedHatCoP](https://twitter.com/RedHatCoP)), OpenShift Evangelism ([Team](https://github.com/openshift-evangelists/kbe/graphs/contributors)), OpenShift.Tips ([Team](https://openshift.tips/about/))
draft: false
weight: 10
---

## API

Sometimes it's useful or necessary to directly [access the Kubernetes API server](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/), for exploratory or testing purposes.

In order to do this, one option is to proxy the API to your local environment, using:

```bash
kubectl proxy --port=8080
Starting to serve on 127.0.0.1:8080

```

Now you can query the API (in a separate terminal session) like so:

```bash
curl http://localhost:8080/api/v1
{
  "kind": "APIResourceList",
  "groupVersion": "v1",
  "resources": [
    {
...
    {
      "name": "services/status",
      "singularName": "",
      "namespaced": true,
      "kind": "Service",
      "verbs": [
        "get",
        "patch",
        "update"
      ]
    }
  ]
}
```

Alternatively, without proxying, you can use `kubectl` directly as follows to achieve the same:

```bash
kubectl get --raw /api/v1
```

Further, if you want to explore the supported API versions and/or resources, you can use the following commands:

```bash
kubectl api-versions
admissionregistration.k8s.io/v1beta1
...
v1

kubectl api-resources
NAME                                  SHORTNAMES       APIGROUP         NAMESPACED   KIND
bindings         true         Binding
componentstatuses                     cs         false        ComponentStatus
configmaps                            cm         true         ConfigMap
...
```

## API resources

```sh
oc api-resources
```

### API resources per API group

```sh
oc api-resources --api-group config.openshift.io -o name
oc api-resources --api-group machineconfiguration.openshift.io -o name
```

## Explain resources

```sh
oc explain pods.spec.containers
```

### Explain resources per api group

```sh
oc explain --api-version=config.openshift.io/v1 scheduler
oc explain --api-version=config.openshift.io/v1 scheduler.spec
oc explain --api-version=config.openshift.io/v1 scheduler.spec.policy
oc explain --api-version=machineconfiguration.openshift.io/v1 containerruntimeconfigs
```
