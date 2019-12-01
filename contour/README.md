Contour
===

[Contour](https://github.com/projectcontour/contour)は`Envoy`を使った`Ingress Controller`である  
`example`では[03-envoy.yaml#L50](https://github.com/projectcontour/contour/blob/master/examples/contour/03-envoy.yaml#L50)にて`DaemonSet`でポートバインドしているので80で疎通可能である

```
$ git clone https://github.com/projectcontour/contour.git
$ kubectl apply -f contour/examples/contour
```

下記のようなリソースが作成される(2019/12/01時点)

```
$ kubectl get all -n projectcontour
NAME                           READY   STATUS      RESTARTS   AGE
pod/contour-6f4746b855-pt5fp   1/1     Running     0          4m55s
pod/contour-6f4746b855-stfcn   1/1     Running     0          4m55s
pod/contour-certgen-sn96x      0/1     Completed   0          4m56s
pod/envoy-z6746                1/1     Running     0          4m55s

NAME              TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/contour   ClusterIP      10.102.22.217   <none>        8001/TCP                     4m55s
service/envoy     LoadBalancer   10.100.70.63    <pending>     80:32012/TCP,443:30571/TCP   4m55s

NAME                   DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/envoy   1         1         1       1            1           <none>          4m55s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/contour   2/2     2            2           4m55s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/contour-6f4746b855   2         2         2       4m55s

NAME                        COMPLETIONS   DURATION   AGE
job.batch/contour-certgen   1/1           11s        4m56s

```

## usage

https://projectcontour.io/docs/v1.0.0/httpproxy/

`HTTPProxy`か`Ingress`を使うことができる  
`HTTPProxy`は`Ingress API`の拡張をしていて`Ingress`における問題点の解決をしようとしている

### HTTPProxy

`v1.0.0`にて`IngressRoute`は非推奨になり、`HTTPProxy`を使うようにとされている  
https://github.com/projectcontour/contour/releases/tag/v1.0.0

```
$ kubectl apply -f sample.yml
$ kubectl apply -f httpproxy.yml
$ kubectl get httpproxy
NAME                                   FQDN                 TLS SECRET   STATUS   STATUS DESCRIPTION
docker-demo-sample-ingress-httpproxy   sample.docker.test                valid    valid HTTPProxy

$ curl localhost -I -H "Host: sample.docker.test"
HTTP/1.1 200 OK
date: Sun, 01 Dec 2019 12:06:33 GMT
content-type: text/html; charset=utf-8
x-envoy-upstream-service-time: 1
server: envoy
transfer-encoding: chunked
```

### Ingress

普通の`kind: Ingress`を使うこともできる

```
$ kubectl apply -f sample.yml
$ kubectl apply -f ingress.yml
$ kubectl get ingress
NAME                         HOSTS                ADDRESS   PORTS   AGE
docker-demo-sample-ingress   sample.docker.test             80      3s

$ curl localhost -I -H "Host: sample.docker.test"
HTTP/1.1 200 OK
date: Sun, 01 Dec 2019 11:56:09 GMT
content-type: text/html; charset=utf-8
x-envoy-upstream-service-time: 1
server: envoy
transfer-encoding: chunked
```