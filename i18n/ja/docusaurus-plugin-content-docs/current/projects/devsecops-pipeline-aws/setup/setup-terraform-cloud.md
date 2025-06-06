---
id: setup-terraform-cloud
title: Setting Up Terraform Cloud
sidebar_position: 3
---

## 概要

Terraform Cloudはインフラストラクチャ管理を簡素化する必須ツールであり、すぐにワークフローの重要な一部となるでしょう。Terraformに不慣れな場合は、先に進む前に時間を取って学習することを強くお勧めします。このガイドでは、Terraform Cloudアカウントの作成と最初の組織の設定について説明します。

## アカウントと組織の作成

1. [Terraform Cloudサインアップページ](https://app.terraform.io/public/signup/account)にアクセスします。
2. **Continue with HCP account**をクリックして登録を進めます。
3. ページ下部の**Sign Up**を選択し、**Continue with GitHub**をクリックしてアカウントを連携します。

   > **注:** サインインページにリダイレクトされた場合は、**Continue with HCP**をクリックし、**Sign in with GitHub**を選択してください。

4. ログイン後、Terraform Cloudダッシュボードに移動します。"DSB"組織がまだ作成されていない場合、空の状態で表示されます：
   ![Terraform Cloudダッシュボード](/img/projects/devsecops-pipeline-aws/setup/image-4.png)

5. **Create Organization**をクリックし、組織名として**DSB**を入力します。コードの設定後、必要に応じてこの組織名を変更できます。