---
id: install-config-gitea
title: Gitea
sidebar_position: 2
---

## 概要

> このインストールは `dsb-hub` 上で行われます。

[Gitea]のドキュメントによると、Giteaはセルフホスト型のオールインワンソフトウェア開発サービスで、Gitホスティング、コードレビュー、チームコラボレーション、パッケージレジストリ、CI/CDを含みます。MITライセンスの下でオープンソースとして提供されており、軽量で使いやすく、高度にカスタマイズ可能な設計となっており、小規模チームから大規模組織まで理想的な選択肢です。

## 前提条件

1. **PostgreSQLのインストール:**

   - 以下のコマンドを実行してPostgreSQLとそのcontribパッケージをインストールします:

     ```bash
     sudo apt install postgresql postgresql-contrib
     ```

   - PostgreSQLユーザーに切り替えます:

     ```bash
     sudo -i -u postgres
     ```

   - PostgreSQLの設定ファイルを更新します:

     ```bash
     sudo nano /etc/postgresql/16/main/postgresql.conf
     ```

   - 下にスクロールして`listen_addresses`設定のコメントを解除し、`localhost`に設定します:

     ```conf
     listen_addresses = 'localhost'
     ```

   - さらに下にスクロールして`password_encryption`設定のコメントを解除し、`scram-sha-256`に設定します:

     ```conf
     password_encryption = scram-sha-256
     ```

2. **PostgreSQLへのログイン:**

   - `postgres`ユーザーとしてPostgreSQLコマンドラインにログインします:

     ```bash
     psql
     ```

3. **データベースの設定:**

   - Gitea用の新しいロール（ユーザー）を安全なパスワードで作成し、そのロールが所有する新しいデータベースを作成します:

     ```sql
     CREATE ROLE gitea WITH LOGIN PASSWORD 'your_password';
     CREATE DATABASE giteadb WITH OWNER gitea TEMPLATE template0 ENCODING 'UTF8' LC_COLLATE 'en_US.UTF-8' LC_CTYPE 'en_US.UTF-8';
     ```

   - `pg_hba.conf`ファイルを更新して、`gitea`ユーザーが`scram-sha-256`を使用して`giteadb`データベースに接続できるようにします:

     ```bash
     sudo nano /etc/postgresql/16/main/pg_hba.conf
     ```

     以下の行を追加します:

     ```conf
     local    giteadb    gitea    scram-sha-256
     ```

4. **データベース接続のテスト:**

   - PostgreSQLサービスを再起動し、Giteaデータベースへの接続をテストします:

     ```bash
     sudo systemctl restart postgresql.service
     psql -U gitea -d giteadb
     ```

5. **Nginxのインストール:**

   - 以下のコマンドを使用してNginxをインストールします:

     ```bash
     sudo apt install nginx
     ```

6. **Nginxの設定:**

   - デフォルトの設定ファイルのリンクを解除します:

     ```bash
     sudo unlink /etc/nginx/sites-enabled/default
     ```

   - リバースプロキシ用の新しい設定ファイルを作成します:

     ```bash
     sudo nano /etc/nginx/sites-available/reverse-proxy
     ```

   - 以下の設定をファイルにコピーし、保存して閉じます:

     ```nginx
     server {
         listen 80;
         server_name localhost;

         location / {
             proxy_pass http://127.0.0.1:3000;
             proxy_set_header Host $host;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header X-Forwarded-Proto $scheme;
         }
     }
     ```

   - 新しいプロキシ設定を有効化し、Nginxを再起動します:

     ```bash
     sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/
     sudo systemctl restart nginx
     ```

## インストール手順

1. **Giteaのインストール:**

   - Snapを使用してGiteaをインストールします:

     ```bash
     sudo snap install gitea
     ```

   - Giteaサービスを開始します:

     ```bash
     sudo snap start gitea
     ```

   - Webフックを設定します:

   ```bash
   sudo nano /var/snap/gitea/common/conf/app.ini

   # ファイル内に以下を追加
   [webhook]
   ALLOWED_HOST_LIST = localhost, 127.0.0.1
   ```

## 設定手順

1. **Giteaの設定:**

   - Webブラウザを開き、サーバーのIPアドレスまたはドメイン名にアクセスします。
   - 画面の指示に従ってGiteaを設定し、プロンプトが表示されたら事前に作成したPostgreSQLデータベースの認証情報を入力します。
     ![Giteaセットアップスクリーンショット](/img/projects/devsecops-home-lab/installation-and-configuration/gitea-init-config.png)

1. **アカウントの作成:**

   - Giteaインスタンスを管理するための管理者アカウントを作成します。
     ![アカウント作成スクリーンショット](/img/projects/devsecops-home-lab/installation-and-configuration/gitea-create-account.png)

## 完了です

Giteaはサーバーに正常にインストールおよび設定されました。ログインして自由に操作を確認してください。
![成功スクリーンショット](/img/projects/devsecops-home-lab/installation-and-configuration/gitea-success-account-creation.png)

<!-- Sources -->

[Gitea]: https://about.gitea.com/products/gitea/