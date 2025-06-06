---
id: devsecops-pipeline-github-actions
title: DevSecOps Pipeline - AWS
description: Build a DevSecOps Pipeline within AWS!
sidebar_position: 3
---

著者: [Damien Burks]

<iframe
  width="100%"
  height="500"
  src="https://www.youtube.com/embed/otleFroshZU?si=otleFroshZU"
  frameborder="0"
  allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen
></iframe>

## 開始前に知っておくべきこと

このプロジェクトは少し費用がかかり、作成したリソースをすべて残しておくとAWSでかなりの請求が発生します。そのため、終了したら**すべて削除する**ことを強く推奨します。**EKSクラスターを実行したままにしないでください。料金が発生します。**

## 前提条件

1. 開始する前に、AWSサービスの動作に関する知識とTerraformの事前知識が必要です。詳細は[クラウドDevSecOpsパイプラインの構築（理論編）](../../blueprint/implementing-cloud-devsecops.md#other-infrastructure-as-code-iac-languages)を参照してください。
1. AWSアカウントを作成済みであることを確認してください。アカウント作成プロセスはこちら：[AWSアカウント作成プロセス](https://aws.amazon.com/resources/create-account/)
1. ローカルマシンに以下がインストールされていることを確認してください：
   - [Python](https://www.python.org/downloads/)
   - [Git](https://git-scm.com/downloads)
   - [Docker](https://docs.docker.com/engine/install/)
   - [Terraform CLI](https://developer.hashicorp.com/terraform/install)

## 概要

AWS内に独自のクラウドネイティブDevSecOpsパイプラインを構築する道を選んだのですか？ようこそ！これは私のお気に入りのプロジェクトの1つで、Terraform Cloudを使用してAWSパイプラインをセットアップする方法を紹介します。[DevSecOpsホームラボ](../devsecops-home-lab/index.md)とは異なり、パイプラインの開発とElastic Kubernetes Service（EKS）へのアプリケーションのデプロイに焦点を当てています。

幸いなことに、皆さんは何もする必要はありません。すべてのコードを事前に開発しておきました。開始前に確認すべき2つのGitHubリポジトリは以下の通りです：

1. DevSecOpsパイプライン: https://github.com/The-DevSec-Blueprint/aws-devsecops-pipeline
1. FastAPIアプリケーション: https://github.com/The-DevSec-Blueprint/awsome-fastapi

## アーキテクチャ図

![アーキテクチャ図](/img/projects/devsecops-pipeline-aws/architecture.drawio.svg)

### アーキテクチャの概要

**非常に**高レベルで見ると、このアーキテクチャはコンテナ化されたアプリケーションをデプロイするために複数のAWSサービスを活用した自動化CI/CDパイプラインを表しています：

1. **AWS CodePipeline**: コード変更のエンドツーエンドのフローを管理し、ビルド、テスト、デプロイの各ステージを自動化します。
2. **AWS CodeBuild**: アプリケーションコードをビルドおよびテストし、デプロイ可能なアーティファクトを生成し、SnykとTrivyを使用してセキュリティスキャンを実行します。
3. **Amazon S3**: ビルド出力やデプロイメントファイルなどのアーティファクトを保存します。
4. **AWS Systems Manager (SSM) Parameter Store**: Snyk用の設定データとシークレットを安全に管理します。
5. **Amazon EKS**: コンテナ化されたワークロードのデプロイメント環境として機能し、スケーラビリティとオーケストレーションを提供します。

**フローの概要**:

- CodePipelineがプロセスを調整します。
- CodeBuildがコードを検証、スキャン、コンパイルし、SSMと連携します。
- アーティファクトはS3に保存されます。
- アプリケーションはEKSクラスターにデプロイされます。

このアーキテクチャは、現代のDevSecOpsワークフローにおいて自動化、セキュリティ、スケーラビリティを確保します。

## 学べること

このガイドを通じて、AWS上で安全なクラウドネイティブDevSecOpsパイプラインを構築・デプロイする実践的な経験を得られます。具体的には、以下の方法を学びます：

- **安全なCI/CDパイプラインの設計と実装:**  
  AWS CodePipeline、CodeBuild、S3、SSM Parameter Store、EKSが連携して、自動化されたエンドツーエンドのワークフローを構築する方法を理解します。

- **パイプラインへのセキュリティスキャンの統合:**  
  SnykやTrivyなどのツールを使用して、本番環境へのデプロイ前にアプリケーションコードとコンテナイメージの脆弱性をスキャンします。

- **Infrastructure as Code（IaC）におけるTerraformの活用:**  
  Terraformを使用してAWSリソースを一貫性を持って大規模にプロビジョニングおよび管理します。

- **コンテナ化されたアプリケーションのAmazon EKSへのデプロイ:**  
  EKS上でコンテナ化されたワークロードを実行し、スケーラビリティと簡素化されたオーケストレーションを確保します。

- **設定とシークレットの安全な管理:**  
  AWS Systems Manager Parameter Storeを使用して機密データを保存および取得し、パイプライン全体でセキュリティのベストプラクティスに従います。

以上の点を踏まえ、**ドキュメントの順序に従って進めてください。そうしないと、エラーが発生したり、混乱したりする可能性があります。**

## 追加参考ガイド

このセクションには、迷った場合や動画が役に立たない場合に備えて、参考になるガイドを記載しています:

| Name                                                                   | Link                                                                                                                 | Type                           |
| ---------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| Building a Cloud-Native DevSecOps Pipeline on AWS with Terraform Cloud | https://medium.com/@gregoryeast1/building-a-cloud-native-devsecops-pipeline-on-aws-with-terraform-cloud-28060c3d9896 | Blog written by [Gregory East] |

[Gregory East]: https://medium.com/@gregoryeast1

[Damien Burks]: https://www.youtube.com/@damienjburks