---
title: "Upgrades Tips by Example"
linkTitle: "Upgrades"
description: "OpenShift CLI upgrades commands by example with tips and tricks from the experts"
date: 2019-06-18T16:42:20+02:00
lastmod: 2019-06-18T16:42:20+02:00
publishdate: 2019-06-18T16:42:20+02:00
author: Red Hat Communities of Practice ([@RedHatCoP](https://twitter.com/RedHatCoP)), OpenShift.Tips ([Team](https://openshift.tips/about/))
draft: false
weight: 41
---

## Upgrade cluster to latest

```sh
oc adm upgrade --to-latest
```

## Force the update to a specific version/hash

* Get the hash of the image version

```sh
CHANNEL='prerelease-4.1'
ARCH='amd64'
curl -sH 'Accept: application/json' "https://api.openshift.com/api/upgrades_info/v1/graph?channel=${CHANNEL}&${ARCH}" | jq .
```

* Apply the update

```sh
oc adm upgrade --allow-explicit-upgrade --force=true --to-image=quay.io/openshift-release-dev/ocp-release@sha256:7e1e73c66702daa39223b3e6dd2cf5e15c057ef30c988256f55fae27448c3b01
```

## Verify the available upgrade versions

Kudos to [Ramon Gordillo](https://github.com/rgordill)

Depending on the OCP version you can upgrade to some specific versions.

For 4.1.10 for amd64:

```sh
curl -s -XGET "https://api.openshift.com/api/upgrades_info/v1/graph?channel=stable-4.1&arch=amd64" --header 'Accept:application/json' |jq '. as $graph | $graph.nodes | map(.version == "4.1.10") | index(true) as $orig | $graph.edges | map(select(.[0] == $orig)[1]) | map($graph.nodes[.])'
```

Output is something similar to:

```json
[
  {
    "version": "4.1.11",
    "payload": "quay.io/openshift-release-dev/ocp-release@sha256:bfca31dbb518b35f312cc67516fa18aa40df9925dc84fdbcd15f8bbca425d7ff",
    "metadata": {
      "description": "",
      "url": "https://access.redhat.com/errata/RHBA-2019:2417",
      "io.openshift.upgrades.graph.release.manifestref": "sha256:bfca31dbb518b35f312cc67516fa18aa40df9925dc84fdbcd15f8bbca425d7ff",
      "io.openshift.upgrades.graph.release.channels": "stable-4.1"
    }
  }
]

```