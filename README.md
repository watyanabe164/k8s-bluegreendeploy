# k8s-bluegreendeploy
https://amateur-engineer-blog.com/kubernetes-blue-green/
を参考に試しました。

# 実行環境
ローカルPC：Windows11
DockerDesktop 4.18.0
WSLターミナルよりminikube起動
```
minikube start
```

# 検証手順
作業ディレクトリ＝"/mnt/c/Users/user/Documents/work/k8s-bluegreendeploy"
1. blue環境をデプロイ
```
kubectl apply -f blue
```
2. 動作確認
```
### リソース確認
kubectl get pod,svc,cm

### ブラウザより動作確認
minikube service myapp-service-blue
#### →出力されたURLをブラウザで表示（青い画面が出たら成功）
```
3. アプリのserviceを作成（中身はblue環境をサービスとして作成してる）
service.ymlを以下の要領で作成
```
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  ports:
  - name: http
    port: 80
  selector:
    app: myapp
    version: blue
  type: NodePort
```
サービス起動
```
kubectl apply -f service.yml
```
4. 動作確認
```
### リソース確認
kubectl get pod,svc,cm

### ブラウザより動作確認
minikube service myapp-service
#### →出力されたURLをブラウザで表示（青い画面が出たら成功）
```
5. green環境をデプロイ
```
kubectl apply -f green
```
6. 動作確認
```
### リソース確認
kubectl get pod,svc,cm

### ブラウザより動作確認
minikube service myapp-service-green
#### →出力されたURLをブラウザで表示（緑の画面が出たら成功）
```
7. アプリのserviceを作成（中身はgreen環境をサービスとして作成してる）
service.ymlを以下の要領で修正
```
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  ports:
  - name: http
    port: 80
  selector:
    app: myapp
    version: green ←この部分をblueからgreenへ修正
  type: NodePort
```
サービス起動
```
kubectl apply -f service.yml
```
8. 動作確認
```
### リソース確認
kubectl get pod,svc,cm

### ブラウザより動作確認
minikube service myapp-service
#### →出力されたURLをブラウザで表示（青ではなく緑の画面が出たら成功）
```
9. Blue環境を削除
```
kubectl delete deploy myapp-blue
```