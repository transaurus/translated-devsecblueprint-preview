---
id: setup-snyk
title: Setting Up Snyk Account
sidebar_position: 2
---

## 概要

このガイドでは、Snykアカウントの作成と、スキャン用に必要なトークンおよび組織IDの生成方法を説明します。動画に沿って進めている場合、このドキュメントでは時間の都合で詳細が省かれた手順をカバーしています。

## アカウント作成プロセス

1. [Snykサインアップページ](https://app.snyk.io/login)にアクセスします。
2. **GitHub**オプションを使用してサインアップし、プロンプトに従ってアカウントを作成します。

   > **注:** サインアッププロセスはシンプルで、最初の段階ではクレジットカードは不要です。

3. 登録が完了すると、Snykダッシュボードにリダイレクトされます:

   ![Snykダッシュボード](/img/projects/devsecops-pipeline-aws/setup/image.png)

## トークンと組織IDの取得

1. ページの左下にある自分の名前をクリックし、**アカウント設定**を選択します。
2. **一般**セクションで、**Auth Token**フィールドを見つけます。**生成**をクリックしてトークンを作成し、メモしておきます。このトークンは後で統合時に必要になります。

   ![Auth Tokenの生成](/img/projects/devsecops-pipeline-aws/setup/image-1.png)

3. **設定**ページに移動し、下にスクロールして**Organization ID**を見つけ、このIDもメモしておきます。環境設定時に必要になります。

   ![Organization ID](/img/projects/devsecops-pipeline-aws/setup/image-2.png)

これらの手順が完了すると、Snykアカウントの準備は完了です！