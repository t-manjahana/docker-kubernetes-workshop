# Chapter 毎の設定について（必読）
# Ingress Controller 用のマニフェストについて

直接 kubectl apply コマンドを使用して公式から直接デプロイすることも可能だが、この講習では公式から直接適用する
```
$ curl -o cluster/ingress-nginx-deploy.yaml https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

- 対象箇所
  - chapter04/cluster/ingress-nginx-deployment.yaml
  - chapter05/cluster/ingress-nginx-deployment.yaml

---

# MySQL パスワードについて
.env.secret.template を .env.secret にコピーし、それぞれのパスワードを指定する。
指定したパスワードが mysql Pod 生成時に使用パスワードとして設定されるため、手動で MySQL にアクセスする際はここで指定したパスワードでログインする。

```
$ cat .env.secret
MYSQL_ROOT_PASSWORD=XXXXXXXXXX   # Root パスワードを指定
MYSQL_PASSWORD=XXXXXXXXXX        # User パスワードを指定
```

- 対象箇所
  - chapter05/.env.secret
---
# Secret の .dockerconfigjson について
Secret マニフェストの `.dockerconfigjson` については、以下の方法で Base64 エンコードした Access Token を張り付ける。
その際の Access Token 取得方法にはいくつかパターンがある。

- 対象箇所
  - chapter04/manifest/secret.yaml
  - chapter05/manifest/config.json

---
### **パターン A : コマンドで Base64 文字列を生成する**
ユーザー名やパーソナルアクセストークンから直接 Base64 化。
```
# ユーザー名とトークン（パスワード）を置換して実行
$ DOCKER_USER="<DockerHubユーザー名>"
$ DOCKER_PASS="<パーソナルアクセストークン>"
$ DOCKER_EMAIL="<メールアドレス>"

# Docker 認証 JSON の作成と Base64 エンコード
$ echo -n "{\"auths\":{\"https://index.docker.io/v1/\":{\"username\":\"$DOCKER_USER\",\"password\":\"$DOCKER_PASS\",\"email\":\"$DOCKER_EMAIL\",\"auth\":\"$(echo -n "$DOCKER_USER:$DOCKER_PASS" | base64)\"}}}" | base64 -w 0
ewoJImF1...<中略>...hlIgp9
```

---
### **パターン B : 手元の ~/.docker/config.json を利用する**
Docker 演習時に docker login した際に生成された設定ファイル（.docker/config.json）をそのまま Base64 化。
**<span style="color: red;">※Windows + WSL2 + Docker Desktop の組み合わせでは、セキュリティ仕様（資格情報マネージャー `desktop.exe`）により、`~/.docker/config.json`内に認証トークンの保存がされないため、パターン A を使用すること</span>**
```
$ cat ~/.docker/config.json | base64 -w 0
ewoJImF1...<中略>...hlIgp9
``` 
