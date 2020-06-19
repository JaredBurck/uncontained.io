---
title: "Replication Controllers Tips by Example"
linkTitle: "Replication Controllers"
description: "OpenShift/K8s CLI replication controller commands by example with tips and tricks from the experts."
date: 2019-02-27
author: Red Hat Communities of Practice ([@RedHatCoP](https://twitter.com/RedHatCoP)), OpenShift Evangelism ([Team](https://github.com/openshift-evangelists/kbe/graphs/contributors))
draft: false
weight: 33
---

A replication controller (RC) is a supervisor for long-running pods.
An RC will launch a specified number of pods called `replicas` and makes
sure that they keep running, for example when a node fails or something
inside of a pod, that is, in one of its containers goes wrong.

Let's create an [RC](https://github.com/openshift-evangelists/kbe/blob/master/specs/rcs/rc.yaml)
that supervises a single replica of a pod:

```bash
kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/master/specs/rcs/rc.yaml
```

You can see the RC and the pod it looks after like so:

```bash
$ kubectl get rc
NAME                DESIRED   CURRENT   READY     AGE
rcex                1         1         1         3m

$ kubectl get pods --show-labels
NAME           READY     STATUS    RESTARTS   AGE    LABELS
rcex-qrv8j     1/1       Running   0          4m     app=sise
```

Note two things here:

- the supervised pod got a random name assigned
(`rcex-qrv8j`)
- the way the RC keeps track of its pods is via the label, here `app=sise`

To scale up, that is, to increase the number of replicas, do:

```bash
$ kubectl scale --replicas=3 rc/rcex

$ kubectl get pods -l app=sise
NAME         READY     STATUS    RESTARTS   AGE
rcex-1rh9r   1/1       Running   0          54s
rcex-lv6xv   1/1       Running   0          54s
rcex-qrv8j   1/1       Running   0          10m

```

Finally, to get rid of the RC and the pods it is supervising, use:

```bash
$ kubectl delete rc rcex
replicationcontroller "rcex" deleted
```

Note that, going forward, the RCs are called [replica sets](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) (RS), supporting set-based selectors. The RS are already in use in the context of [deployments](/deployments/).