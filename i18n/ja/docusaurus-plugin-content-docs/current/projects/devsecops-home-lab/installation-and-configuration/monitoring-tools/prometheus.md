---
id: install-config-prometheus
title: Prometheus
sidebar_position: 4
---

## 概要

> このインストールは `dsb-node-01` 上で行われます。

[PrometheusのWebサイト]によると、PrometheusはSoundCloudで最初に開発されたオープンソースのシステム監視およびアラートツールキットです。信頼性と拡張性を考慮して設計されており、設定されたターゲットから定期的にメトリクスを収集し、ルール式を評価し、結果を表示し、必要に応じてアラートをトリガーします。このガイドでは、Docker Composeを使用してシステムにPrometheusをインストールおよび設定する手順を説明します。

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

1. Prometheus用のDocker-Composeファイルの作成

   - 必要なディレクトリとDocker Composeファイルを作成します:

     ```bash
     mkdir -p ~/apps/prometheus
     touch ~/apps/prometheus/docker-compose.yml
     ```

2. Docker-Compose設定のコピーと保存

   - `docker-compose.yml` ファイルに以下の設定を使用します:

     ```yaml
     version: "3.7"

     services:
       prometheus:
         image: prom/prometheus:latest
         container_name: prometheus
         volumes:
           - prometheus_data:/prometheus
           - ./prometheus.yml:/etc/prometheus/prometheus.yml
         ports:
           - "9090:9090"
         restart: always
         network_mode: host

     volumes:
       prometheus_data:
     ```

   - nanoまたはお好みのテキストエディタでファイルを開き、設定を貼り付けます:

     ```bash
     nano ~/apps/prometheus/docker-compose.yml
     ```

   - ファイルを保存して閉じます。

3. カスタム `prometheus.yml` ファイルの作成

   - Prometheusディレクトリに `prometheus.yml` ファイルを作成します:

     ```bash
     touch ~/apps/prometheus/prometheus.yml
     ```

   - `prometheus.yml` ファイルに以下の設定を追加します:

     ```yaml
     global:
       scrape_interval: 15s
       evaluation_interval: 15s

     scrape_configs:
       - job_name: "cadvisor"
         static_configs:
           - targets: ["localhost:8080"]

       - job_name: "node_exporter"
         static_configs:
           - targets: ["localhost:9100"]

       - job_name: "jenkins"
         metrics_path: /prometheus/
         static_configs:
           - targets: ["<your_ip_address>:8080"]
     ```

     > **注**: この設定は、PrometheusがcAdvisor、Node Exporter、およびJenkinsからメトリクスを収集するように設定します。`<your_ip_address>` をJenkinsサーバーの実際のIPアドレスに置き換えてください。

4. Docker-ComposeでPrometheusを実行

   - Prometheusディレクトリに移動し、コンテナを実行します:

     ```bash
     cd ~/apps/prometheus
     docker-compose up -d
     ```

5. Prometheusが実行中であることを確認

   - Webブラウザで `http://localhost:9090` にアクセスし、Prometheusが起動していることを確認します。

     ![Prometheusダッシュボード](/img/projects/devsecops-home-lab/installation-and-configuration/prometheus-dashboard.png)

## 完了

システムにPrometheusを正常にインストールおよび設定しました。Prometheusサーバーは現在稼働中であり、設定されたターゲットからメトリクスを収集し、システムのパフォーマンスと健全性に関する洞察を提供する準備が整っています。

<!-- Sources -->

[PrometheusのWebサイト]: https://prometheus.io/docs/introduction/overview/