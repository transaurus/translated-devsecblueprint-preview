---
id: install-config-nexus
title: Nexus
sidebar_position: 4
---

## 概要

> このインストールは `dsb-hub` 上で行われます。

[Nexus Repository Manager](https://www.sonatype.com/products/sonatype-nexus-oss) は、Docker、Maven、npm など様々な形式のコンポーネントやビルド成果物を管理するツールです。SonarQube の JDK 要件との競合を避けるため、Docker Compose を使用して Nexus をインストールします。Nexus は Docker イメージの管理に使用され、Docker Hub からのイメージプロキシ、ローカルキャッシュ、安全な取得管理を可能にします。

## インストール手順

1. **Nexus用の新しいディレクトリを作成:**

   ```bash
   mkdir -p apps/nexus/nexus-data
   sudo chown -R 200 apps/nexus/nexus-data
   ```

2. **Docker Compose YAMLファイルを作成:**

   ```bash
   cd apps/nexus
   touch docker-compose.yml
   ```

3. **`docker-compose.yml` に以下の内容を追加:**

   ```yaml
   version: "3"
   services:
     nexus:
       image: sonatype/nexus3
       restart: always
       volumes:
         - "./nexus-data:/nexus-data"
       ports:
         - "8081:8081"
         - "8082:8082"
         - "8085:8085"
   volumes:
     nexus-data:
       driver: local
   ```

4. **アプリケーションを実行:**

   ```bash
   docker compose up -d
   ```

5. **以下のURLにアクセスしてアプリケーションが起動していることを確認:**

   ```text
   http://your-ip-address:8081
   ```

![Nexusが起動中](/img/projects/devsecops-home-lab/installation-and-configuration/nexus-initial-view.png)

## 設定手順

1. **「Sign In」ボタンをクリックし、管理者パスワードを確認:**

   ```bash
   cat nexus-data/admin.password
   ```

   ![管理者パスワード](/img/projects/devsecops-home-lab/installation-and-configuration/nexus-found-admin-pw.png)

2. **パスワードを使用してログインし、初期設定を完了:**

   - パスワードを変更
   - 必要に応じて匿名アクセスを有効化

   ![ログイン](/img/projects/devsecops-home-lab/installation-and-configuration/nexus-change-admin-pw.png)

3. **管理者としてUIに移動し、新しいリポジトリを作成:**
   ![リポジトリ作成](/img/projects/devsecops-home-lab/installation-and-configuration/nexus-create-repository.png)

4. **新しいリポジトリで「Docker proxy」を選択し、以下の情報を入力:**

   1. 名前: `docker-proxy`
   2. リモートストレージプロキシURL: `https://registry.hub.docker.com`
   3. Dockerインデックス: `Docker Hub`
   4. 匿名プルを有効化
   5. HTTPを `8082` に設定

5. **ユーザー名 `nx-anonymous` でローカルユーザーを作成し、設定を完了:**

   ![ユーザー作成](/img/projects/devsecops-home-lab/installation-and-configuration/nexus-create-user.png)

## 完了

Nexusサーバーのセットアップが正常に完了しました。