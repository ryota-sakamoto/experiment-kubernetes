`ArgoCD`は`argocd-server`でのTLS末端処理を必須としているためどうやって動かせばいいかよくわからない  
LBでSSLオフロードして`NodePort`で試した時は前は上手く動いていたが`Ingress`を使うようにしたら動かなくなった  
そこでここでは`--insecure`を指定することで誤魔化す

[insecureフラグ](https://github.com/argoproj/argo-cd/blob/0cfe1cdedf2b05f0c3a3ed13ff73a66d427f5749/cmd/argocd-server/commands/root.go#L98)はここにある

```
[argocd.sakamo-dev.test] -> [contour(envoy)] -> [httpproxy/argocd-httpproxy] -> [svc/argocd-server]
```

下記のコマンドで`HTTPProxy`でホスト名で解決できるようにして`/etc/hosts`にも書いておく  
ちなみに`edit`した後はローリングアップデートされるので当然pod名は変わるのでメモしておかないとログインできなくなる

```
$ kubectl apply -f httpproxy.yml
$ kubectl -n argocd edit deploy/argocd-server

$ cat /etc/hosts | grep sakamo
127.0.0.1 argocd.sakamo-dev.test
```

```
 39         app.kubernetes.io/name: argocd-server
 40     spec:
 41       containers:
 42       - command:
 43         - argocd-server
 44         - --insecure
 45         - --staticassets
 46         - /shared/app
 47         image: argoproj/argocd:v1.3.0
```