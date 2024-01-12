# GKE

## CLI
GCPのCLIが利用可能
コマンドベースでGCPのリソースの作成・更新・削除などが可能

```
$gcloud config list
> default config

# gcpではすべてのリソースがいづれかのPJに属する必要がある
# デフォルトのPJを設定する
$gcloud config set project [pj-id]
>Updated property [core/project]
```

## create cluster

```
# create autopilot cluster
gcloud container clusters create-auto gke-handson --location=asia-northeast1
```
**autopilotモード**
GKEクラスタのノード、インフラをGoogle側で管理するマネージドなクラスタ

通常の作成コマンドの場合、自身でノードのサイズなどを設定する必要がある。

参考）
GCP無償枠ではSSD_TOTAL_GBの制限があり、必要となる900GBを用意できないためエラーとなる。
```
$gcloud container clusters create <cluster-name> --location=asia-northeast1
```
![](2024-01-07-14-31-50.png)

このため、無償枠でコマンドライン経由のクラスタ作成時にはautopilotモードにて作成する必要がある。（オプションによって制御が可能かもしれないが未調査）

# kubernetes
ここからk8s共通部分（をGKEでやるだけ）

[構成]
以下の構成で動くことを想定する

- nginx
    - front(http://front)
    - back(http://back:YYY)

バックエンドAPIは同一ドメインを想定
例）
nginx-gip/api/v1/login
へのリクエストがfrontのコンテンツから送られたときにbackにルーティングされるような動作

[課題]
- nginxを自前で構成する
ローカル開発でやっているnginxの設定をそのまま本番でも利用したいので、自前のnginx用のPod-Serviceを公開するのがよさそう。

## Deployment
```
apiVersion: apps/v1
kind: Deployment
metadata
```

## Service
```
# front service resource
apiVersion: v1
kind: Service
metadata:
  name: front-service
  labels:
    app: front
spec:
  kind: LoadBalancer
  ports:
    name: "http"
    protocol: "TCP"
    port: 80
```