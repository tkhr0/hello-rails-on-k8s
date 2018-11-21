# hello-rails-on-k8s

k8s の勉強

Rails を k8s 環境で動かす

## helm
https://github.com/helm/helm

## Hands on
### 0. Preparation

ローカルだと Ingress で Service を公開することはできない  
クラスタ外からの HTTP リクエストを Service にルーティングするために [nginx_ingress_controller](https://github.com/kubernetes/ingress-nginx/) をデプロイしておく

```bash
$ kubectl -f apply https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.16.2/deploy/mandatory.yaml
$ kubectl -f apply https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.16.2/deploy/provider/cloud-generic.yaml
```

Check deployed services and pods

```bash
$ kubectl get svc,pod --namespace ingress-nginx
NAME                           TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/default-http-backend   ClusterIP      10.110.86.111    <none>        80/TCP                       1d
service/ingress-nginx          LoadBalancer   10.105.208.212   localhost     80:32414/TCP,443:31696/TCP   1d

NAME                                            READY   STATUS    RESTARTS   AGE
pod/default-http-backend-7fb86fb47d-6th9z       1/1     Running   1          1d
pod/nginx-ingress-controller-78bd49949c-5vh5r   1/1     Running   0          1d
```

### 1. Build containers

```bash
$ docker build -f .docker/containers/app/Dockerfile   -t hello-rails-on-k8s-app:latest .
$ docker build -f .docker/containers/nginx/Dockerfile -t hello-rails-on-k8s-nginx:latest .
$ docker build -f .docker/containers/db/Dockerfile    -t hello-rails-on-k8s-db:latest .
```

### 2. Deploy by kubectl
#### Deploy
For application
```bash
$ kubectl apply -f .docker/kubernetes/webserver.yml
$ kubectl apply -f .docker/kubernetes/dbserver.yml
$ kubectl apply -f .docker/kubernetes/ingress.yml
```

For logging
```bash
$ kubectl apply -f .docker/kubernetes/fluentd.yml
$ kubectl apply -f .docker/kubernetes/elasticsearch.yml
$ kubectl apply -f .docker/kubernetes/kibana.yml
```

#### Check
Request to rails  

```bash
curl http://localhost -H "Host: hello-rails-on-k8s.local"
```

Access to Kibana  
http://localhost:30050

> Note:  
>   `curl http://localhost -H "Host: hello-rails-on-k8s.local"` は、  
>   `http://localhost` に対してリクエストするんだけど、あたかも `http://hello-rails-on-k8s.local` としてリクエストしたように見せかけられる  
>   ref: [curlをhost指定して実行する - するめとめがね](http://tm8r.hateblo.jp/entry/20120820/1345435560)

#### Delete objects
```bash
$ kubectl delete -f .docker/kubernetes/webserver.yml
$ kubectl delete -f .docker/kubernetes/dbserver.yml
$ kubectl delete -f .docker/kubernetes/fluentd.yml
$ kubectl delete -f .docker/kubernetes/elasticsearch.yml
$ kubectl delete -f .docker/kubernetes/kibana.yml
$ kubectl delete -f .docker/kubernetes/ingress.yml
```

### 3. Deploy by helm
#### Deploy
```bash
$ helm install --name hello-rails-on-k8s .helm/stable/hello-rails-on-k8s/
```

#### Check
Request to rails  
```bash
$ curl http://localhost -H "Host: hello-rails-on-k8s.local"
```

#### Delete objects
```bash
$ helm delete hello-rails-on-k8s
# and purge
$ helm del --purge hello-rails-on-k8s
```
