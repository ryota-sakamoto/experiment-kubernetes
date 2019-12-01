ArgoCD
===

[Argo CD](https://github.com/argoproj/argo-cd)は`Kubernetes`における`GitOps`を実現するためのCDツールである

```
$ kubectl create namespace argocd
$ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

下記のようなリソースが作成される(2019/12/02時点)

```
$ kubectl get all -n argocd
NAME                                                 READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-75d4f978bd-9qdlz   1/1     Running   0          99s
pod/argocd-dex-server-5695f4b657-8vtjn               1/1     Running   0          99s
pod/argocd-redis-8c568b5db-9lzpd                     1/1     Running   0          99s
pod/argocd-repo-server-5c59bf6844-8wdwv              1/1     Running   0          99s
pod/argocd-server-77cb9df44-gmwfs                    1/1     Running   0          99s

NAME                            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service/argocd-dex-server       ClusterIP   10.107.129.73    <none>        5556/TCP,5557/TCP   99s
service/argocd-metrics          ClusterIP   10.101.174.161   <none>        8082/TCP            99s
service/argocd-redis            ClusterIP   10.100.45.192    <none>        6379/TCP            99s
service/argocd-repo-server      ClusterIP   10.97.211.142    <none>        8081/TCP,8084/TCP   99s
service/argocd-server           ClusterIP   10.104.252.198   <none>        80/TCP,443/TCP      99s
service/argocd-server-metrics   ClusterIP   10.109.18.40     <none>        8083/TCP            99s

NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-application-controller   1/1     1            1           99s
deployment.apps/argocd-dex-server               1/1     1            1           99s
deployment.apps/argocd-redis                    1/1     1            1           99s
deployment.apps/argocd-repo-server              1/1     1            1           99s
deployment.apps/argocd-server                   1/1     1            1           99s

NAME                                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-application-controller-75d4f978bd   1         1         1       99s
replicaset.apps/argocd-dex-server-5695f4b657               1         1         1       99s
replicaset.apps/argocd-redis-8c568b5db                     1         1         1       99s
replicaset.apps/argocd-repo-server-5c59bf6844              1         1         1       99s
replicaset.apps/argocd-server-77cb9df44                    1         1         1       99s
```

ここでは外部からアクセスするために`port-forward`を使う

```
$ kubectl port-forward service/argocd-server 8080:80 -n argocd
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
```