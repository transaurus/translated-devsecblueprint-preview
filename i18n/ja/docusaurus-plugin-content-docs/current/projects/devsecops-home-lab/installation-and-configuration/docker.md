---
id: install-config-docker
title: Docker
sidebar_position: 1
---

## 概要

> このインストールは両方のマシンで行われます。

[Dockerの公式サイト]によると、Dockerはアプリケーションの開発、配布、実行のためのオープンプラットフォームです。Dockerを使用することで、アプリケーションをインフラストラクチャから分離し、ソフトウェアを迅速に提供できます。Dockerを利用すれば、アプリケーションを管理するのと同じ方法でインフラストラクチャを管理できます。Dockerのコードの配布、テスト、デプロイの方法論を活用することで、コードの記述から本番環境での実行までの遅延を大幅に削減できます。

## Dockerインストール手順

1. **パッケージインデックスの更新**

   まず、既存のパッケージリストを更新します:

   ```bash
   sudo apt-get update
   ```

2. **必要なパッケージのインストール**

   aptがHTTPS経由でリポジトリを使用できるようにするために必要なパッケージをインストールします:

   ```bash
   sudo apt-get install \
       ca-certificates \
       curl \
       gnupg \
       lsb-release
   ```

3. **Docker公式GPGキーの追加**

   Dockerの公式GPGキーをシステムに追加します:

   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

4. **安定版リポジトリのセットアップ**

   以下のコマンドを使用してDockerの安定版リポジトリをセットアップします:

   ```bash
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

5. **Docker Engineのインストール**

   パッケージインデックスを再度更新し、Docker Engineと`containerd`、`docker-compose`をインストールします:

   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-compose
   ```

6. **Dockerインストールの確認**

   インストール後、Dockerが正しくインストールされ、実行されていることを確認します:

   ```bash
   sudo docker --version
   ```

   このコマンドはインストールされたDockerのバージョンを返すはずです。

7. **Dockerサービスの起動と有効化**

   Dockerが起動時に開始されるようにします:

   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

8. **非rootユーザーとしてDockerを管理（オプション）**

   デフォルトでは、Dockerコマンドは`sudo`で実行する必要があります。非rootユーザーとしてDockerコマンドを実行したい場合は、ユーザーを`docker`グループに追加する必要があります:

   ```bash
   sudo usermod -aG docker $USER
   sudo usermod -aG docker jenkins
   ```

   このコマンドを実行した後、ログアウトして再度ログインするか、以下のコマンドを実行してグループメンバーシップを適用します:

   ```bash
   newgrp docker
   ```

9. **Dockerインストールのテスト**

   簡単なDockerコンテナを実行してDockerのインストールをテストします:

   ```bash
   docker run hello-world
   ```

   このコマンドはテストイメージをダウンロードし、コンテナ内で実行し、確認メッセージを表示します。

10. **（オプション）追加のDockerツールのインストール**

まだ含まれていない場合は、Docker Composeもインストールすると良いでしょう:

```bash
sudo apt-get install docker-compose-plugin
```

## Dockerレジストリインストール手順

独自のDockerレジストリをローカルにセットアップすることで、Docker Hubのようなサードパーティサービスに依存せずにDockerイメージをプライベートにホストできます。以下は、マシンまたはサーバー上でローカルDockerレジストリをセットアップして使用する手順です。

1. **必要なディレクトリとファイルの作成**

   ホームディレクトリに移動し、Dockerレジストリ用のディレクトリを作成します:

   ```bash
   cd ~
   mkdir -p apps/docker
   cd apps/docker
   touch docker-compose.yml
   ```

1. **`docker-compose.yml`の設定**

   `docker-compose.yml`ファイルを以下の内容で編集します:

   ```yaml
   version: "3.8"

   services:
     registry:
       image: registry:2.8.2
       ports:
         - "5000:5000"
       environment:
         REGISTRY_HTTP_HEADERS_Access-Control-Allow-Origin: "[http://registry.example.com]"
         REGISTRY_HTTP_HEADERS_Access-Control-Allow-Methods: "[HEAD,GET,OPTIONS,DELETE]"
         REGISTRY_HTTP_HEADERS_Access-Control-Allow-Credentials: "[true]"
         REGISTRY_HTTP_HEADERS_Access-Control-Allow-Headers: "[Authorization,Accept,Cache-Control]"
         REGISTRY_HTTP_HEADERS_Access-Control-Expose-Headers: "[Docker-Content-Digest]"
         REGISTRY_STORAGE_DELETE_ENABLED: "true"
         REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /var/lib/registry
       volumes:
         - ./registry-data:/var/lib/registry

     ui:
       image: joxit/docker-registry-ui:latest
       ports:
         - "8082:80"
       environment:
         - SINGLE_REGISTRY=true
         - REGISTRY_TITLE=Docker Registry UI
         - DELETE_IMAGES=true
         - SHOW_CONTENT_DIGEST=true
         - NGINX_PROXY_PASS_URL=http://<your_dsb_hub_ip_address>:5000
         - SHOW_CATALOG_NB_TAGS=true
         - CATALOG_MIN_BRANCHES=1
         - CATALOG_MAX_BRANCHES=1
         - TAGLIST_PAGE_SIZE=100
         - REGISTRY_SECURED=false
         - CATALOG_ELEMENTS_LIMIT=1000
       depends_on:
         - registry

   volumes:
     registry-data:
   ```

1. **Dockerレジストリのデプロイ**

   Docker Composeを使用してDockerレジストリとUIをデプロイします:

   ```bash
   docker compose up -d
   ```

1. **Dockerデーモンの設定**

   セキュアでないレジストリとやり取りできるようにするため、Dockerデーモンの設定を更新します:

   ```bash
   sudo nano /etc/docker/daemon.json
   ```

   以下の内容を追加します:

   ```json
   {
     "insecure-registries": ["<your_dsb_hub_ip_address>:5000"]
   }
   ```

1. **Dockerサービスの再起動**

   Dockerデーモンの設定を更新した後、変更を適用するためにDockerを再起動します:

   ```bash
   sudo systemctl restart docker
   ```

1. **追加のDockerノードの設定（該当する場合）**

   `dsb-hub-01`などの追加のDockerノードを使用する場合、同様の設定を適用する必要があります:

   ```bash
   sudo nano /var/snap/docker/current/config/daemon.json
   ```

   以下の内容を追加します:

   ```json
   {
     "log-level": "error",
     "insecure-registries": ["<your_dsb_hub_ip_address>:5000"]
   }
   ```

   その後、Dockerサービスを再起動します:

   ```bash
   sudo snap restart docker
   ```

## 完了です

サーバー上でのDockerおよびDockerレジストリの設定とインストールが完了しました。

<!-- Sources -->

[Docker's Website]: https://docs.docker.com/get-started/overview/