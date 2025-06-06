---
id: install-config-cadvisor
title: cAdvisor
sidebar_position: 4
---

## 概要

> このインストールは `dsb-node-01` 上で行われます。

[cAdvisorのGitHubリポジトリ]によると、cAdvisor（Container Advisor）はGoogleが開発したオープンソースツールで、実行中のコンテナのリソース使用状況とパフォーマンス特性に関する洞察を提供することを目的としています。このツールは実行中のコンテナに関する情報を収集、集計、処理、エクスポートするため、コンテナ化された環境を監視するための貴重なツールとなります。このガイドでは、Docker Composeを使用してシステムにcAdvisorをインストールおよび設定する手順を説明します。

## インストール手順

1. **必要なディレクトリの作成**

   まず、`apps`ディレクトリに移動し、cAdvisor用の新しいディレクトリを作成します：

   ```bash
   cd apps
   mkdir cadvisor
   ```

   次に、Dockerライブラリディレクトリが存在しない場合は作成します：

   ```bash
   cd /var/lib
   mkdir docker
   chown -R root:root docker
   ```

2. **Docker Composeファイルの作成**

   `cadvisor`ディレクトリに戻り、`docker-compose.yml`ファイルを作成します：

   ```bash
   cd ~/apps/cadvisor
   touch docker-compose.yml
   ```

3. **Docker Composeファイルの設定**

   `docker-compose.yml`ファイルを編集し、以下の設定を追加します：

   ```yaml
   version: "3.8"

   services:
     cadvisor:
       image: gcr.io/cadvisor/cadvisor
       container_name: cadvisor
       privileged: true
       restart: unless-stopped
       ports:
         - "8080:8080"
       volumes:
         - /:/rootfs:ro
         - /var/run:/var/run:ro
         - /sys:/sys:ro
       network_mode: host
   ```

4. **cAdvisorのデプロイ**

   最後に、Docker Composeを使用してcAdvisorをデプロイします：

   ```bash
   docker-compose up -d
   ```

## 完了

おめでとうございます！cAdvisorのインストールと設定が正常に完了しました。cAdvisorサービスは現在実行中であり、ホストマシンのポート8080経由でアクセスできます。このセットアップにより、実行中のコンテナのパフォーマンスメトリクスをリアルタイムで監視および視覚化することが可能になります。

<!-- Sources -->

[cAdvisorのGitHubリポジトリ]: https://github.com/google/cadvisor