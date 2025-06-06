---
id: config-app-proxy
title: Nginx Reverse Proxy
sidebar_position: 5
---

## 概要

> このインストールは `dsb-node-01` で行われます。

このガイドでは、Nginxを使用してアプリケーションのリバースプロキシを設定する方法を説明します。これにより、着信トラフィックをより効果的に管理し、別のポートで動作しているアプリケーションにリクエストを転送できるようになります。

## インストール手順

1. **Nginxのインストール**

まず、サーバーにNginxをインストールする必要があります。これは以下のコマンドで行えます：

```bash
sudo apt install nginx
```

このコマンドは、Nginxと必要な依存関係をインストールします。

## 設定手順

Nginxのインストール後、アプリケーションのリバースプロキシとして機能するように設定する必要があります。

1. **デフォルト設定の削除:**

   まず、競合を避けるためにデフォルトのNginx設定ファイルのリンクを解除します：

   ```bash
   sudo unlink /etc/nginx/sites-enabled/default
   ```

2. **新しい設定ファイルの作成:**

   次に、リバースプロキシ設定用の新しい設定ファイルを作成します：

   ```bash
   sudo nano /etc/nginx/sites-available/reverse-proxy
   ```

   開いたファイルに、以下の設定を貼り付けます：

   ```nginx
   server {
       listen 80;
       server_name localhost;

       location / {
           client_max_body_size 1000M;
           proxy_pass http://127.0.0.1:8080;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

   この設定は、Nginxにポート80（デフォルトのHTTPポート）でリッスンし、すべての着信トラフィックを `http://127.0.0.1:8080` で動作しているアプリケーションに転送するように指示します。また、アプリケーションのロギングやセキュリティ目的で有用なさまざまなヘッダーを設定します。

3. **新しい設定の有効化:**

   `sites-enabled` ディレクトリへのシンボリックリンクを作成して、新しいリバースプロキシ設定を有効にします：

   ```bash
   sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/
   ```

4. **Nginxの再起動:**

   最後に、新しい設定を適用するためにNginxを再起動します：

   ```bash
   sudo systemctl restart nginx
   ```

## 完了です

Nginxリバースプロキシの設定が完了しました！