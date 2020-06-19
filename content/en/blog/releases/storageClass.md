---
title: "StorageClass Tips by Example"
linkTitle: "StorageClass"
description: "OpenShift CLI storageclass commands by example with tips and tricks from the experts"
date: 2019-06-18T16:42:20+02:00
lastmod: 2019-06-18T16:42:20+02:00
publishdate: 2019-06-18T16:42:20+02:00
author: Red Hat Communities of Practice ([@RedHatCoP](https://twitter.com/RedHatCoP)), OpenShift.Tips ([Team](https://openshift.tips/about/))
draft: false
weight: 39
---

## Get default StorageClass name

```sh
oc get sc -o jsonpath='{.items[?(@.metadata.annotations.storageclass\.kubernetes\.io/is-default-class=="true")].metadata.name}'
```
