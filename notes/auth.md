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
