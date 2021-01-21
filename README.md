# kubectl-plugin-socks-proxy

This is a kubectl plugin that creates a local SOCKS5 proxy through which you can access to pods/services in a Kubernetes cluster.

What the plugin actually does is that it create a SOCKS proxy server Pod in a Kubernetes cluster and forwards a local port (default:1080) to the proxy. So you can access to pods/servcies in Kuberenetes cluster by using the local port as SOCKS5 proxy like this:

```bash
curl --socks5-hostname localhost:1080 http://httpbin.default.svc.cluster.local/headers
```

## Installing

This is a way to install kubectl-socks-proxy through [krew](https://krew.sigs.k8s.io/). After installing krew by following [this](https://krew.sigs.k8s.io/docs/user-guide/setup/install/), you can install kubectl-socks-proxy like this:

```sh
$ kubectl krew install socks-proxy
```

Once it's installed, run:
```sh
$ kubectl krew list

PLUGIN        VERSION
access-matrix a09c0597c96e008147ae1a120b16690a1401dfd243c6c5629ba396a34797d1f0
krew          dc2f2e1ec8a0acb6f3e23580d4a8b38c44823e948c40342e13ff6e8e12edb15a
socks-proxy   3b7091d90a1ad082c078a406f7b455a9f5686996d3c273efe589c5ae4c445bd8

$ kubectl socks-proxy
```

## How to use

### Usage

```TXT
Usage:
  kubectl socks-proxy

Options:
  -n, --namespace <namespace>   Namespace to create SOCKS5 proxy server in

  -p, --port <local port>       Local port to be forwarded to the SOCKS5 proxy server (Pod).
                                A client connects to this port, then the connection is forwarded to the
                                SOCKS5 proxy server, which is then forwareded to the destination server

  --skip-cleanup-proxy          Skip cearning up SOCKS5 proxy pod
                                Default: Cleaning up SOCKS5 proxy pod at the end

  -h, --help                    Show this message
```

### Use Socks5 Proxy in Curl

Suppose you deploy a sample app in a Kubernetes cluster like this
```bash
git clone https://github.com/yokawasa/kubectl-plugin-socks-proxy.git
cd kubectl-plugin-socks-proxy
kubectl apply -f sample-apps/party-clippy.yaml

kubectl get svc

NAME           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes     ClusterIP   10.0.0.1       <none>        443/TCP   96d
party-clippy   ClusterIP   10.0.5.226     <none>        80/TCP    1m
```

First of all, create a local SOCKS5 proxy (default: 1080 port) by running `kubectl socks-proxy` like this:
```
kubectl socks-proxy

using: namespace=default
using: port=1080
Creating Socks5 Proxy (Pod)...
pod/socks5 created
Forwarding from 127.0.0.1:1080 -> 1080
Forwarding from [::1]:1080 -> 1080
```

Now you can access services/pods in the Kubernetes cluster from a local machine by using the local port as SOCKS proxy in Curl
```
# Service name
curl --socks5-hostname localhost:1080 party-clippy
# Service name
curl --socks5-hostname localhost:1080 http://party-clippy
# FQDN
curl --socks5-hostname localhost:1080 http://party-clippy.default.svc.cluster.local
```

```
  _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |  /
    || ||
    |\_/|
    \___/
```


## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/yokawasa/kubectl-plugin-socks-proxy
