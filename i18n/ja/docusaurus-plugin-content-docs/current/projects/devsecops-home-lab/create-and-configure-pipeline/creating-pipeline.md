---
id: create-jenkins-piepline
title: Creating & Configuring Jenkins Pipeline
description: A step-by-step guide to creating a Jenkins pipeline for OWASP Juice Shop using Gitea, SonarQube, and SSH keys.
sidebar_position: 2
---

## ステップ1: Giteaでアクセストークンを設定する

1. Giteaで、右上のユーザーアバターをクリックし、**設定** > **アプリケーション**を選択します。

   ![アプリケーション](/img/projects/devsecops-home-lab/create-configure-pipeline/gitea-manage-access-tokens.png)

2. トークン名（例: `Jenkins`）を入力し、`Private`に設定し、すべての権限をREAD/WRITEとして選択します。**トークンを生成**をクリックします。

   ![トークンの生成](/img/projects/devsecops-home-lab/create-configure-pipeline/gitea-configure-token-permissions.png)

3. トークンをコピーし、安全な場所に保管します。このトークンはJenkinsの認証に使用されます。

## ステップ2: Jenkinsに必要なプラグインをインストールする

1. Jenkinsにログインします。**ダッシュボード**から、**Jenkinsの管理** > **プラグインの管理**をクリックします。

   ![プラグインの管理](/img/projects/devsecops-home-lab/create-configure-pipeline/jenkins-manage-plugins.png)

2. **利用可能なプラグイン**で、以下を検索してインストールします:

   - **Gitea**
   - **SonarQube Scanner**
   - **Prometheus Metrics**

   ![プラグインのインストール](/img/projects/devsecops-home-lab/create-configure-pipeline/jenkins-install-plugins.png)

## ステップ3: Jenkinsパイプラインを作成する

1. Jenkinsの**ダッシュボード**から、**新しいジョブ**をクリックします。

   ![新しいジョブ](/img/projects/devsecops-home-lab/create-configure-pipeline/jenkins-dashboard.png)

2. **Organization Folder**を選択し、名前を付けます（例: `OWASP Juice Shop Pipeline`）。

   ![Organization Folder](/img/projects/devsecops-home-lab/create-configure-pipeline/jenkins-organization-folder.png)

3. **Repository Sources**までスクロールし、**Gitea Organization**を選択します。

   ![リポジトリソース](/img/projects/devsecops-home-lab/create-configure-pipeline/jenkins-configure-gitea-pipeline.png)

4. **Credentials**で、**追加**をクリックします。Organization Folder名を選択し、**Kind**を**Gitea Personal Access Token (PAT)**に設定し、Giteaで生成したトークンを貼り付けます。

   ![Gitea PAT](/img/projects/devsecops-home-lab/create-configure-pipeline/jenkins-configure-gitea-pat.png)

5. **Owner**をGiteaのユーザー名に設定し、**適用**と**保存**をクリックします。

   ![設定の保存](/img/projects/devsecops-home-lab/create-configure-pipeline/jenkins-config-gitea-organization.png)

## ステップ4: GiteaでWebhookを設定する

1. Jenkinsで、**Jenkinsの管理** > **システム設定**に移動します。**Gitea Servers**までスクロールし、Giteaサーバーの詳細を追加します。

   ![Giteaサーバー](/img/projects/devsecops-home-lab/create-configure-pipeline/jenkins-config-gitea-server.png)

   - **ブランチを検出**: PRとして提出されたブランチまたはmaster/mainブランチのみ。
   - **originからのプルリクエストを検出**: 現在のプルリクエストリビジョンと、現在のターゲットブランチリビジョンとマージされたプルリクエストの両方。

2. **適用**と**保存**をクリックします。

3. Giteaで、プロジェクトに移動し、**設定** > **Webhooks**をクリックします。

   ![Webhooks](/img/projects/devsecops-home-lab/create-configure-pipeline/gitea-config-webhook.png)

4. **Webhookを追加**をクリックし、**Gitea**を選択します。フォームに記入します:

   - **URL**: `<http://localhost:8080/gitea-webhook/post>`
   - **メソッド**: `POST`
   - **コンテンツタイプ**: `application/json`
   - **ブランチフィルター**: `*`

   ![Webhookの追加](/img/projects/devsecops-home-lab/create-configure-pipeline/gitea-add-webhook-jenkins.png)

5. **Webhookを追加**をクリックして設定を保存します。

## ステップ5: SSHキーを設定する

1. ローカルマシン（DSB Hub）でSSHキーペアを生成します：

   ```bash
   ssh-keygen -t rsa -b 4096 -C "jenkins@dsb-hub.com"
   ```

   ```plaintext
   Generating public/private rsa key pair.
   Enter file in which to save the key (/home/<your_username>/.ssh/id_rsa):
   Enter passphrase (empty for no passphrase):
   Enter same passphrase again:
   Your identification has been saved in /home/<your_username>/.ssh/id_rsa
   Your public key has been saved in /home/<your_username>/.ssh/id_rsa.pub
   The key fingerprint is:
   SHA256:iIitv6/AYHsTTND7ooLJtG0M2NZrOWMer7E0E6hZ8XI jenkins@dsb-hub.com
   The key's randomart image is:
   ```

2. 公開鍵をリモートサーバーにコピーします：

   ```bash
   ssh-copy-id <your_username>@dsb-node-01.local
   ```

3. Jenkinsで、**Manage Jenkins** > **Credentials** > **System** > **Global Credentials (unrestricted)**に移動します。**Add Credentials**を選択し、**SSH Username with private key**を選びます。

   ![SSH Credentials](/img/projects/devsecops-home-lab/create-configure-pipeline/jenkins-config-ssh-username.png)

4. フォームに必要事項を入力し、**Create**をクリックしてSSH認証情報を保存します。

## まとめ

これで、OWASP Juice ShopプロジェクトのJenkinsパイプラインをGitea、SonarQube、Dockerと統合してセットアップできました。このパイプラインは、コード品質チェック、セキュリティスキャン、デプロイメントを処理するように構成されており、アプリケーションが開発ライフサイクル全体を通じて高い標準を維持できるようになります。