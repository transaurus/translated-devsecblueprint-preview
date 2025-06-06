---
id: deploying-infrastructure-code
title: Deploying and Configuring Your DevSecOps Pipeline
sidebar_position: 1
---

## 概要

ここまで来て、ようやくTerraform Cloudを使用してインフラストラクチャをデプロイする段階に到達しました。このガイドでは、プロジェクトに必要なDevSecOpsパイプラインを作成、設定、デプロイする手順を説明します。

## 設定手順

### Terraform Cloudのセットアップ

1. Terraform Cloudにログインし、**DSB組織**を選択します。
2. **新規作成**ボタンをクリックして新しいプロジェクトを作成します。必要に応じて名前と説明を入力します。
3. **ワークスペース**に移動し、作成したプロジェクトを選択して**続行**をクリックします。
4. **CLI駆動ワークフロー**を選択します（GitHub Actionsに必要です）。
5. ワークスペース名を`dsb-aws-devsecops-eks-cluster`と入力します。任意の説明を追加し、**作成**をクリックします。
6. 同じ手順を繰り返して、`dsb-aws-devsecops-pipelines`という名前の別のワークスペースを作成します。

このプロセスの最後には、2つのワークスペースが作成されているはずです。以下は、組織内での表示例です（実行ステータスは適用されていません）：

![ワークスペース概要](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image.png)

#### Terraform Cloudでのシークレットの設定

1. `dsb-aws-devsecops-pipelines`ワークスペースに移動し、**変数**を選択します。

   ![ワークスペース変数](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-1.png)

2. **ワークスペース変数**で、以下の2つの機密変数を作成します：

   - `SNYK_ORG_ID`
   - `SNYK_TOKEN`

   これらの変数にそれぞれの値を入力します。最終的な設定は以下のようになります：
   ![設定済み変数](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-2.png)

### GitHub Actionsを使用した変更のデプロイ

ワークスペースが設定されたので、GitHub Actionsを使用して変更をデプロイできます。

1. GitHubにログインし、フォークしたプロジェクト`aws-devsecops-pipelines`を開きます。
2. **Actions**に移動し、`.github/workflows/main.yml`をクリックします。
   ![ワークフローファイル](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-3.png)
3. 右側の**Run Workflow**ドロップダウンを選択し、**Run Workflow**をクリックします。これにより、パイプラインがトリガーされ、以下の処理が行われます：

   - リポジトリをチェックアウトします。
   - Terraform Cloudで変更を計画および適用します。
   - EKSクラスターと`AWSOME-FastAPI`のDevSecOpsパイプラインをデプロイします。

   > **注記**: このプロセスには約30分かかる場合があります。EKSクラスターの作成には時間がかかるため、この間は離れていても構いません。

4. プランが正常に適用されたことを確認します。GitHubとTerraform Cloudの両方で成功したビルドが表示されるはずです。以下は結果の例です：

   **GitHubパイプライン実行**:
   ![GitHub実行結果](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-4.png)

   **Terraform Cloudデプロイメント**:
   ![Terraformデプロイメント結果](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-5.png)

   **AWS概要**:
   ![AWS概要](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-6.png)

### CodeStar接続の設定とテスト

インフラストラクチャがデプロイされたので、次のステップはAWSとGitHubをリンクするCodeStar接続を設定することです。これにより、Pythonプロジェクト`AWSOME-FastAPI`への変更が自動的に検出およびデプロイされます。

1. AWSの**CodePipelineダッシュボード**に移動します。
2. **設定 > 接続**をクリックし、`dsb-github-connection`という名前を選択します。ステータスはおそらく`保留中`になっており、これがパイプラインが`失敗`状態になっている理由です。接続は`有効`である必要があります。

   ![保留中の接続](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-7.png)

3. **保留中の接続を更新**をクリックします。ブラウザのポップアップが表示されます。
4. **新しいアプリをインストール**をクリックします。

   ![アプリのインストール](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-8.png)

5. GitHubユーザー名を選択して、GitHub用AWSコネクタをインストールします。

   ![GitHubユーザー名の選択](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-9.png)

6. GitHub接続ダッシュボードにリダイレクトされたら、**接続**をクリックします。接続ステータスが`利用可能`と表示されるはずです。

   ![利用可能な接続](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-10.png)

これらの手順を完了すると、パイプラインは完全に動作可能な状態になり、GitHubリポジトリからの変更を検出してデプロイできるようになります。