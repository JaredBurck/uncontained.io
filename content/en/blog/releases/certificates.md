---
title: "Certificates Tips by Example"
linkTitle: "Certificates"
description: "OpenShift CLI certificates commands by example with tips and tricks from the experts"
date: 2019-06-18T16:42:20+02:00
lastmod: 2019-06-18T16:42:20+02:00
publishdate: 2019-06-18T16:42:20+02:00
author: Red Hat Communities of Practice ([@RedHatCoP](https://twitter.com/RedHatCoP)), OpenShift.Tips ([Team](https://openshift.tips/about/))
draft: false
weight: 11
---

## Sign all the pending `csr`

```sh
oc get csr -o name | xargs oc adm certificate approve
```

## Authenticate users using TLS certificates

Create a new user `OCP_USERNAME` to perform operations against the API server `OCP_API_SERVER`.

```sh
export OCP_USERNAME="alice"
export OCP_API_SERVER="https://api.example.com:6443"
```

Generate a private key and a CSR for the new user.

```sh
mkdir ${OCP_USERNAME}

openssl req -new -nodes -subj "/CN=${OCP_USERNAME}" \
  -keyout ${OCP_USERNAME}/private.key -out ${OCP_USERNAME}/request.csr
```

Authenticate to Openshift API with an user with permissions to create `CertificateSigningRequest` objects (e.g. kube-admin).

```sh
oc login --server=${OCP_API_SERVER}
```

Create a `CertificateSigningRequest` to sign the CSR by the kube-apiserver CA.

```sh
cat <<EOF | oc apply -f -
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: tls-auth-${OCP_USERNAME}
spec:
  signerName: "kubernetes.io/kube-apiserver-client"
  request: $(cat ${OCP_USERNAME}/request.csr | base64 | tr -d '\n')
  usages:
    - digital signature
    - key encipherment
    - client auth
  extra:
    scopes.authorization.openshift.io:
      - user:full
EOF
```

Approve the pending CSR.

```sh
oc adm certificate approve tls-auth-${OCP_USERNAME}
```

Get the user certificate from the signed CSR.

```sh
oc get csr tls-auth-${OCP_USERNAME} -o jsonpath="{.status.certificate}" |\
  base64 -d > ${OCP_USERNAME}/certificate.pem
```

Get the CA chain for the API server.

```sh
oc get cm kube-apiserver-server-ca \
  -o jsonpath="{.data.ca-bundle\.crt}" -n openshift-kube-apiserver > api-ca.pem
```

Create a kubeconfig to authenticate the new user using the TLS certificate.

```sh
oc adm create-kubeconfig \
  --kubeconfig=${OCP_USERNAME}/kubeconfig \
  --user=${OCP_USERNAME} \
  --client-certificate=${OCP_USERNAME}/certificate.pem \
  --client-key=${OCP_USERNAME}/private.key \
  --certificate-authority=api-ca.pem \
  --public-master=${OCP_API_SERVER} \
  --master=${OCP_API_SERVER}
```

Authenticate using the new kubeconfig.

```sh
export KUBECONFIG="${OCP_USERNAME}/kubeconfig"
```

Verify the new user can make operations against the API server.

```sh
oc whoami
```

## Verify the API certificates

```sh
echo | openssl s_client -connect api.ocp4.example.com:6443 | openssl x509 -noout -text
```

## Extract etcd CA

```sh
oc get secrets -n openshift-config etcd-signer -o "jsonpath={.data['tls\.crt']}" |  base64 -d | openssl x509 -text
```