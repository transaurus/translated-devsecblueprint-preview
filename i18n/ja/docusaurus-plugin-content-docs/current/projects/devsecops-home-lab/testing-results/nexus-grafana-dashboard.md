---
id: install-nexus-grafana-dashboard
title: Installing a Grafana Dashboard for Nexus
sidebar_position: 3
---

## 概要

このインストール手順は、監視目的でGrafana内にNexusダッシュボードを作成・設定するためのものです。

## 前提条件

1. **Nexus Repository Managerの監視設定**

   - Nexus Repository ManagerがPrometheus向けにメトリクスを公開するよう設定されていることを確認してください。
   - Nexusインストールディレクトリ内の`etc/`にある`nexus.properties`ファイルを編集します:

     ```bash
     sudo nano ./apps/nexus/nexus-data/etc/nexus.properties
     ```

     Prometheusメトリクスを有効化するため、以下のプロパティを追加:

     ```bash
     nexus.prometheus.enabled=true
     ```

   - 変更を反映させるためNexusを再起動:

     ```bash
     docker compose down && docker compose up -d
     ```

2. **Prometheusの設定**

   - Prometheus設定ファイル(`prometheus.yml`)にNexusをターゲットとして追加:

     ```yaml
     scrape_configs:
       - job_name: "nexus"
         static_configs:
           - targets: ["<nexus_host>:<nexus_port>"]
     ```

     `<nexus_host>`はNexusサーバーのホスト名またはIPアドレス、`<nexus_port>`はNexusが動作しているポート（デフォルトは8081）に置き換えてください。

   - Nexusメトリクスの収集を開始するためPrometheusを起動/再起動します。

## インストール手順

1. **GrafanaでNexusダッシュボードをインポート**

- NexusトラフィックをGrafanaで可視化する手順:
  1. Grafanaインスタンスにログイン
  2. 左メニューの**Create**（プラスアイコン）をクリックし、**Import**を選択
  3. ダッシュボードID **16459** を入力、またはURLを貼り付け: [https://grafana.com/grafana/dashboards/16459-infra-nexus/](https://grafana.com/grafana/dashboards/16459-infra-nexus/)
  4. **Load**をクリック
  5. Nexusからメトリクスを収集しているPrometheusデータソースを選択
  6. **Import**をクリックしてダッシュボードをGrafanaに追加

1. **監視と調整**

- インポート後、ダッシュボードでNexusのトラフィックとパフォーマンスメトリクスを監視できます。
- クエリの調整や追加パネルの設置など、必要に応じてダッシュボードをカスタマイズしてください。
- パフォーマンス閾値やトラフィック条件に基づくアラートをGrafanaで設定し、プロアクティブに通知を受け取れます。

## 完了

この設定により、GrafanaでNexus Repository Managerの健全性と使用状況を効果的に監視できるようになります。