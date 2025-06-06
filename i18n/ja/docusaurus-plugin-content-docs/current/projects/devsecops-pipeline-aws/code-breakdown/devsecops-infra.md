---
id: devsecops-terraform-code
title: DevSecOps Terraform Code - Explained
sidebar_position: 1
---

## 概要

環境設定とシークレットの作成が完了したら、DevSecOpsパイプラインのインフラストラクチャを定義するTerraformコードについて詳しく見ていきましょう。このガイドでは、システムの動作を完全に理解できるよう、重要なコンポーネントについて詳細に説明します。

## コード概要

関連するコードはすべて`terraform`フォルダにあり、相互接続された2つのTerraformワークスペースで構成されています:

1. **EKSクラスター**
2. **パイプライン**

### EKSクラスター ワークスペース

このワークスペースでは、Elastic Kubernetes Service（EKS）クラスター、ノードグループ、および必須のクラスターリソースをプロビジョニングします。パイプラインのワークスペースと比べて範囲は小さいですが、Kubernetesベースのデプロイメントの基盤となります。コードベースは[こちら](https://github.com/The-DevSec-Blueprint/aws-devsecops-pipeline/tree/main/terraform/eks-cluster)で確認できます。

- **ファイル**:
  - `main.tf`: EKSクラスター、ノードグループ、ネットワークコンポーネント、デフォルトサブネットを定義
  - `variables.tf`: クラスター名、リージョン、ノード仕様などの入力変数を設定
  - `outputs.tf`: EKSクラスター名やエンドポイントなどの重要な情報を出力

### パイプライン ワークスペース

このワークスペースには、CI/CDパイプラインを設定するためのインフラストラクチャが含まれています。フォルダには複数のファイルがありますが、`main.tf`ファイルが中核となります。コードベースは[こちら](https://github.com/The-DevSec-Blueprint/aws-devsecops-pipeline/tree/main/terraform/eks-cluster)で確認できます。以下に、詳細な主要要素を説明します:

#### GitHub接続設定

- **リソース**: `aws_codestarconnections_connection`
- **目的**:
  - AWS CodePipelineとGitHubリポジトリ間の安全な接続を確立
  - トレーサビリティのため、`random_id`リソースを使用して一意の接続名を生成
  - プロバイダタイプを"GitHub"として設定

#### デフォルトS3バケット設定

- **モジュール**: `default_bucket`
- **目的**:
  - CodePipelineアーティファクトを保存するためのS3バケットをプロビジョニング
  - 変数を使用してバケット命名規則を標準化
  - ビルドおよびデプロイメントアーティファクトの安全で一元化されたストレージを確保

#### EKSクラスター設定

- **モジュール**: `cluster_auth`
- **目的**:
  - EKSクラスターの認証およびRBAC設定を管理
  - CodeBuild IAMロールに`system:masters`グループを関連付けることで、クラスターとの連携を許可
  - クラスターに管理権限を持つIAMユーザー（"your_name"）を追加（この部分は実際のアカウント名に置き換える必要があります）

#### FastAPIパイプライン設定

- **モジュール**: `awsome_fastapi_pipeline`
- **目的**:
  - "AWSOME FastAPI"プロジェクトのCI/CDパイプラインを構築
  - GitHub接続を活用してリポジトリからソースコードを取得
  - S3バケットとEKSクラスターと統合し、シームレスなデプロイメントを実現

#### 主要パイプラインパラメータ

- **GitHub統合**:
  - GitHub接続ARNをCodePipelineに動的にリンク
  - リポジトリ詳細を設定:
    - リポジトリ: `The-DevSec-Blueprint/awsome-fastapi`
    - ブランチ: `main`
- **ビルドとデプロイメント**:
  - Buildspec: `buildspecs/awsome-fastapi/build.yml`に配置
  - Deployspec: `buildspecs/awsome-fastapi/deploy.yml`に配置
  - ビルド環境:
    - コンピュートタイプ: `BUILD_GENERAL1_SMALL`
    - イメージ: `aws/codebuild/standard:5.0`
    - 環境タイプ: `LINUX_CONTAINER`
    - コンテナ化ビルドのため特権モードを有効化
- **セキュリティスキャン**:
  - Snykを統合し、`SNYK_ORG_ID`と`SNYK_TOKEN`変数を使用して脆弱性スキャンを実施

これらのTerraform構成の目的と構造を理解することで、インフラストラクチャのプロビジョニングから安全で自動化されたCI/CDワークフローの実現まで、DevSecOpsパイプラインがどのように機能するかについて、より明確な全体像を把握できるようになります。