# httpbin-k8s

Sample Deployment YAML for httpbin on Kubernetes


## Create k8s (KIND) cluster

Build a k8s cluster using [KIND](https://kind.sigs.k8s.io/)

```sh
kind create cluster --image kindest/node:v1.30.0 --config=kind/kind-cluster.yaml
```

Check if the cluster has been created as expected

```sh
$ kind get cluster

kind
```

Check the cluster info using `kubectl cluster-info` and `kubectl get node` commands

```sh
$ kubectl cluster-info

Kubernetes control plane is running at https://127.0.0.1:62546
CoreDNS is running at https://127.0.0.1:62546/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.


$ kubectl get nodes

NAME                        STATUS   ROLES           AGE     VERSION
kind-1.30.0-control-plane   Ready    control-plane   9m18s   v1.30.0
kind-1.30.0-worker          Ready    <none>          8m56s   v1.30.0
```

## Deploy the httpbin app

Deploy the httpbin app to the testns namespace in the cluster.

First of all, create `testns` namespace

```sh
$ kubectl create namespace testns

namespace/testns created
```

Then, finally deploy httpbin app to the testns namespace

```sh
$ kubectl apply -f manifests/httpbin.yaml -n testns

serviceaccount/httpbin created
service/httpbin created
deployment.apps/httpbin created
```

Check service and pods are running

```sh
$ kubectl get svc,pods -n testns

NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/httpbin   ClusterIP   10.96.241.31   <none>        8000/TCP   115s

NAME                           READY   STATUS    RESTARTS   AGE
pod/httpbin-69f764cd68-k4nct   1/1     Running   0          115s
```

## Access the httpbin

First, port porward svc/httpbin port of 8000 to localhost:8080

```sh
$ kubectl port-forward svc/httpbin 8080:8000 -n testns
```
Finally access the endpoint

```sh
$ curl http://localhost:8080/get


{
  "args": {}, 
  "headers": {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", 
    "Accept-Encoding": "gzip, deflate, br, zstd", 
    "Accept-Language": "en-US,en;q=0.9,ja;q=0.8", 
    "Connection": "keep-alive", 
    "Cookie": "locale=en-US", 
    "Host": "localhost:8080", 
    "Sec-Ch-Ua": "\"Chromium\";v=\"136\", \"Google Chrome\";v=\"136\", \"Not.A/Brand\";v=\"99\"", 
    "Sec-Ch-Ua-Mobile": "?0", 
    "Sec-Ch-Ua-Platform": "\"macOS\"", 
    "Sec-Fetch-Dest": "document", 
    "Sec-Fetch-Mode": "navigate", 
    "Sec-Fetch-Site": "none", 
    "Sec-Fetch-User": "?1", 
    "Upgrade-Insecure-Requests": "1", 
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/136.0.0.0 Safari/537.36"
  }, 
  "origin": "127.0.0.1", 
  "url": "http://localhost:8080/get"
}
```
