---
id: install-config-grafana
title: Grafana
sidebar_position: 4
---

## 概要

> このインストールは `dsb-node-01` 上で行われます。

[Grafanaの公式ウェブサイト]によると、Grafanaは監視と可観測性のためのオープンソースプラットフォームです。メトリクスがどこに保存されていても、それらをクエリ、可視化、アラート設定し、理解することを可能にします。Grafanaは、さまざまなソースからのデータを統合したダッシュボードを作成、探索、共有するための強力でエレガントな方法を提供し、DevOpsチームがインフラストラクチャ、アプリケーション、サービスをリアルタイムで監視するための必須ツールとなっています。

## 前提条件

1. **Dockerのインストール**

   - Snapを使用してDockerをインストールします:

     ```bash
     sudo snap install docker
     ```

2. **Dockerグループの作成とユーザーの追加**

   - Dockerグループを作成し、ユーザーを追加します:

     ```bash
     sudo groupadd docker
     sudo usermod -aG docker $USER
     sudo reboot
     ```

## インストール手順

1. **Grafana用のDocker-Composeファイルの作成**

   - 必要なディレクトリとDocker-Composeファイルを作成します:

     ```bash
     mkdir -p ~/apps/grafana
     touch ~/apps/grafana/docker-compose.yml
     ```

2. **Docker-Compose設定のコピーと保存**

   - `docker-compose.yml` ファイルに以下の設定を使用します:

     ```yaml
     version: "3.7"

     services:
       grafana:
         image: grafana/grafana:latest
         container_name: grafana
         ports:
           - "3000:3000"
         environment:
           - GF_SECURITY_ADMIN_PASSWORD=your_admin_password
           - GF_SECURITY_ADMIN_USER=your_admin_user
         volumes:
           - grafana_data:/var/lib/grafana
         restart: always
         network_mode: host

     volumes:
       grafana_data:
     ```

   - nanoまたはお好みのテキストエディタでファイルを開き、設定を貼り付けます:

     ```bash
     nano ~/apps/grafana/docker-compose.yml
     ```

   - ファイルを保存して閉じます。

3. **Docker-ComposeでGrafanaを実行**

   - Grafanaディレクトリに移動し、コンテナを実行します:

     ```bash
     cd ~/apps/grafana
     docker-compose up -d
     ```

4. **Grafanaの動作確認**

   - ウェブブラウザで `http://localhost:3000` にアクセスし、Grafanaが起動していることを確認します。

   ![Grafanaログイン](/img/projects/devsecops-home-lab/installation-and-configuration/grafana-login-dashboard.png)

## 設定手順

1. **ダッシュボードへのログイン**

   - 管理者ユーザー名とパスワードを使用してGrafanaダッシュボードにログインします。

   ![Grafanaダッシュボード](/img/projects/devsecops-home-lab/installation-and-configuration/grafana-admin-pass-login.png)

2. **管理者ユーザー名の更新**

   - プロファイルをクリックし、管理者ユーザー名を更新します。

   ![ユーザー名の更新](/img/projects/devsecops-home-lab/installation-and-configuration/grafana-profile-config.png)

3. **管理者パスワードの更新**

   - セキュリティ強化のため、パスワードを変更します。

   ![パスワードの更新](/img/projects/devsecops-home-lab/installation-and-configuration/grafana-change-admin-pass.png)

## 完了

システムにGrafanaを正常にインストールおよび設定しました。

<!-- Sources -->

[Grafanaの公式ウェブサイト]: https://grafana.com/docs/grafana/latest/getting-started/what-is-grafana/