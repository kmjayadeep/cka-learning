# Authentication

There are two types of users -> serviceaccounts and normal users

Normal users are - managed by administrator distributing private keys, file list with user nd pass, some external auth etc.
A valid certificate signed by cluster's CA is considered a valid user. The user name is given by Common NAme (CN) and group is given by Organization(O) in the certificate.
You can create a certificate signing request using kubectl and approve them to create users

serviceaccounts are created and managed through kubernetes api. They are bound to namespace and used by pods to talk to api.
They have secrets created in the corresponding namespace which contain a JWT token to authenticate. It is mounted to pods automatically.
Clients know where to find this secret in-order to authenticate with api server

If not authenticated, the client is treated as anonymous user

# Plugins

Authentication plugins does the authentication and associate the request with username, group, UID etc. for Authorization.
There can be multiple authentication plugins enabled

* X509 Client certs
* Static token file - contains token, username, uid, groups (optional).  This token can be passed as an Authorization bearer token header

Bootstrap tokens -> A limited token used to bootstrap a cluster. Used by `kubeadm`

serviceaccounts tokens ->  Used api server's private key to sign bearer tokens for serviceaccounts

OpenID connect tokens -> External authentication returns a token which can be verified by api server. Api server verfication is entirely based on the token (no calling to external service)
So it cannot be revoked

Webhook token -> Use external  Webhook to verify token

NOte: use `--as=username` and `--as-group=groupname` to run kubectl commands as a particular user and group

# Creating new normal user

Generate private key And CSR

```
openssl genrsa -out jd.key 2048
openssl req -new -key jd.key -out jd.csr
```

Organization name will be user group and Common Name will be user name

Create CSR in kubernetes

```
CSR=$(cat jd.csr | base64 | tr -d "\n")
```

``` 
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: jd
spec:
  request: $CSR
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
EOF
```

```
❯ k get csr
NAME   AGE   SIGNERNAME                            REQUESTOR          CONDITION
jd     9s    kubernetes.io/kube-apiserver-client   kubernetes-admin   Pending
```

Approve

```
k certificate approve jd
```

Get certificate

```
k get csr jd -o jsonpath='{.status.certificate}' | base64 -d > jd.crt
```

## Create Role and rolebinding to test

```
k create role developer --resource=pods --verb=get --verb=list
k create rolebinding jddeveloper --role=developer --user=jd
k get po --as=jd
```

## Create a kubeconfig file

```
k konfig export kind-kind > kind.yaml
```

```
k config set-credentials jd \
  --client-certificate=jd.crt \
  --client-key=jd.key \
  --embed-certs=true \
  --kubeconfig=kind.yaml
```

```
k config set-context kind-kind \
  --cluster=kind-kind \
  --user=jd \
  --kubeconfig=kind.yaml
```

```
k get po --kubeconfig kind.yaml
```

# Aerviceaccounts

* serviceaccount admission controller - inject default sa
* token controller - manages serviceaccount secret
* serviceaccount controller - makes sure default sa exists in all namespaces
