---
id: config-secrets
title: Configuring Secrets and Environment Variables
sidebar_position: 5
---

## 概要

基礎的なセットアップが完了したので、このガイドではGitHubとTerraform Cloud内のシークレットと環境変数の設定方法について説明します。

## Terraform Cloudの設定

1. Terraform Cloudにログインし、「DSB」組織を選択します。
2. 左側のメニューで「Settings」 > 「Variable Sets」をクリックします。以下のような画面が表示されます：

   ![Variable Sets](/img/projects/devsecops-pipeline-aws/setup/image-6.png)

3. 「Create Organization Variable Set」をクリックし、以下の詳細を入力します：
   - **Name**: 変数セットの意味のある名前を入力します。
   - **Description**: 明確にするための簡単な説明を追加します。
   - **Variable Set Scope**: 「Apply to all projects and workspaces」を選択します（必要に応じて後で変更可能です）。
4. 「Variables」セクションまでスクロールし、「Add Variable」をクリックします。以下のキーを追加し、**Sensitive**としてマークします：
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
     > **注:** 以前に保存したスプレッドシートの認証情報を使用してください。
5. 変数を追加した後、変数セットは以下のようになります：

   ![Final Variable Set](/img/projects/devsecops-pipeline-aws/setup/image-7.png)

## GitHubの設定

リポジトリをフォークした後、`aws-devsecops-pipeline`リポジトリ内でGitHub Actionsに必要なシークレットを設定する必要があります。これらのシークレットにより、mainブランチに更新がプッシュされた際に自動デプロイが可能になります。

1. GitHubにログインし、`aws-devsecops-pipeline`リポジトリを開きます。
2. **Settings** > **Secrets and Variables**（「Security」セクション内）に移動します。
3. **Actions**をクリックし、**New Repository Secret**を選択します。
4. シークレット名を`TF_API_TOKEN`とし、以前に生成したTerraform Cloudのトークンを貼り付けます。

これらの手順が完了すると、リポジトリと環境が完全に設定され、使用準備が整います。