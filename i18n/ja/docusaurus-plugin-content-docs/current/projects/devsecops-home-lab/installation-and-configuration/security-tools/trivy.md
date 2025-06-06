---
id: install-config-trivy
title: Trivy
sidebar_position: 4
---

## 概要

> このインストールは `dsb-hub` 上で行われます。

[TrivyのGitHubリポジトリ]によると、Trivyは包括的で使いやすいオープンソースの脆弱性スキャナーです。OSパッケージ、コンテナイメージ、ファイルシステム、Gitリポジトリの脆弱性を検出します。さらに、Trivyは設定の問題やハードコードされたシークレットも検出できるため、DevSecOpsプラクティスにおいて不可欠なツールです。このガイドでは、システムにTrivyをインストールして設定する手順を説明します。

## インストール手順

1. パッケージの設定とインストール

   - 必要なパッケージをインストールし、Trivyリポジトリのキーを追加します:

     ```bash
     sudo apt-get install wget apt-transport-https gnupg
     wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
     ```

   - Trivyリポジトリをソースリストに追加します:

     ```bash
     echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
     ```

   - パッケージリストを更新し、Trivyをインストールします:

     ```bash
     sudo apt-get update
     sudo apt-get install trivy
     ```

2. Trivyが正常にインストールされたか確認

   - `trivy` コマンドを実行して、Trivyが正しくインストールされ動作していることを確認します:

     ```bash
     trivy
     ```

   - 以下のような出力が表示されれば、Trivyがインストールされており、使用方法が示されます:

     ```text
     コンテナイメージ、ファイルシステム、Gitリポジトリの脆弱性スキャナー、および設定の問題やハードコードされたシークレットを検出します

     使用方法:
       trivy [グローバルフラグ] コマンド [フラグ] ターゲット
       trivy [コマンド]

     例:
       # コンテナイメージをスキャン
       $ trivy image python:3.4-alpine

       # tarアーカイブからコンテナイメージをスキャン
       $ trivy image --input ruby-3.1.tar

       # ローカルファイルシステムをスキャン
       $ trivy fs .

       # サーバーモードで実行
       $ trivy server

     スキャンコマンド:
       config      設定ファイルの不備をスキャン
       filesystem  ローカルファイルシステムをスキャン
       image       コンテナイメージをスキャン
       kubernetes  [実験的] Kubernetesクラスタをスキャン
       repository  リポジトリをスキャン
       rootfs      rootfsをスキャン
       sbom        SBOMの脆弱性とライセンスをスキャン
       vm          [実験的] 仮想マシンイメージをスキャン

     管理コマンド:
       module      モジュールを管理
       plugin      プラグインを管理
       vex         [実験的] VEXユーティリティ

     ユーティリティコマンド:
       clean       キャッシュファイルを削除
       completion  指定されたシェルの自動補完スクリプトを生成
       convert     Trivy JSONレポートを別の形式に変換
       help        コマンドのヘルプを表示
       server      サーバーモード
       version     バージョンを表示

     フラグ:
           --cache-dir string          キャッシュディレクトリ (デフォルト "/home/damien/.cache/trivy")
       -c, --config string             設定パス (デフォルト "trivy.yaml")
       -d, --debug                     デバッグモード
       -f, --format string             バージョンフォーマット (json)
           --generate-default-config   デフォルト設定をtrivy-default.yamlに書き出し
       -h, --help                      trivyのヘルプを表示
           --insecure                  安全でないサーバー接続を許可
       -q, --quiet                     プログレスバーとログ出力を抑制
           --timeout duration          タイムアウト (デフォルト 5m0s)
       -v, --version                   バージョンを表示
     ```

## 完了

Trivyがインストールされ、コンテナイメージやファイルシステムなどの脆弱性スキャンに使用できるようになりました。Trivyを使用することで、アプリケーションを本番環境にデプロイする前に、既知の脆弱性がない安全な状態であることを確認できます。

<!-- Sources -->

[TrivyのGitHubリポジトリ]: https://github.com/aquasecurity/trivy