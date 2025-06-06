---
id: install-grafana-jenkins-dashboard
title: Installing a Grafana Dashboard for Jenkins
sidebar_position: 2
---

## 概要

このインストールプロセスは、監視目的でGrafanaにJenkinsのパフォーマンスとヘルス概要ダッシュボードを設定するためのものです。

## 前提条件

1. **ダッシュボードの検索:**

   - [こちら](https://grafana.com/grafana/dashboards/?search=jenkins)からJenkins関連のGrafanaダッシュボードを閲覧できます。

2. **Jenkinsパフォーマンスとヘルス概要ダッシュボードのインポート:**

   - 特定のダッシュボードは[こちら](https://grafana.com/grafana/dashboards/9964-jenkins-performance-and-health-overview/)から見つけることができます。

3. **ダッシュボードのインポート:**

   - 提供されたリンクからダッシュボードのJSONをダウンロードまたはコピーします。
   - Grafanaでダッシュボードセクションに移動します。
   - **インポート**をクリックします。
   - JSONコードを貼り付けるか、JSONファイルをアップロードします。
   - インポートプロセスを完了します。

   ![ダッシュボード画像](/img/projects/devsecops-home-lab/testing-results/grafana-import-dashboard.png)

## インストール手順

1. **Grafanaへのアクセス:**

   - WebブラウザでGrafanaインスタンスを開きます。

2. **ダッシュボードのインポート:**

   - **ダッシュボード**セクションに移動します。
   - **インポート**ボタンをクリックします。
   - 事前に取得したJSONコードを貼り付けるか、JSONファイルをアップロードします。
   - プロンプトに従ってインポートを完了します。

## 設定手順

1. **ダッシュボードのカスタマイズ:**

   - インポート後、特定の監視ニーズに合わせてダッシュボードをカスタマイズできます。

1. **設定の保存:**

   - ダッシュボードに加えた変更を必ず保存してください。

## 結論

Jenkinsのパフォーマンスとヘルス概要用のGrafanaダッシュボードが設定され、使用準備が整いました！