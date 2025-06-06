---
id: executing-pipeline
title: Running the Pipeline and Analyzing Outputs
sidebar_position: 2
---

## 概要

インフラストラクチャのデプロイと検証が完了したら、次のステップはパイプラインを実行し、その出力を分析することです。このガイドでは、パイプラインの実行方法、セキュリティスキャン結果の確認、デプロイされたアプリケーションのテスト方法について説明します。

## パイプラインの実行

1. **CodePipeline ダッシュボード**を開き、`awsome-fastapi`パイプラインに移動します。パイプライン名をクリックしてください。
   ![パイプライン ダッシュボード](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-11.png)
2. **Release Change**をクリックし、続いて**Release**をクリックして確認します。この操作により、パイプラインが以下の処理を実行します：

   - GitHubリポジトリから最新のコードを取得。
   - プロジェクトのビルド。
   - テストとセキュリティスキャンの実行。
   - EKSクラスターへのアプリケーションのデプロイ。

   > **注**: パイプラインの処理には10〜30分かかる場合があります。この時間を利用して休憩し、完了後に戻ってきてください。

## 結果の確認

パイプラインが完了したら、セキュリティスキャンの結果を確認します。以下はSnykとTrivyの結果の例です：

![Snyk 結果](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-12.png)

> **Snyk 結果**

![Trivy 結果](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-13.png)

> **Trivy 結果**

- Trivyのスキャン結果は広範で、包括的に対処するのが難しい場合があります。まずは最も重大な問題に焦点を当ててください。
- 特定の脆弱性でパイプラインを失敗させたい場合は、`AWSOME-FastAPI`リポジトリの`buildspec.yml`ファイルを適宜設定できます。

  > **注**: 脆弱性は時間とともに変化するため、定期的なレビューと更新が重要です。

## APIアプリケーションのテスト

1. **EKSクラスター ダッシュボード**を開き、`dsb-devsecops-cluster`を選択します。
2. **リソース**の下で、**サービスとネットワーキング > サービス**に移動し、`awsome-fastapi`サービスを探します。以下の画面が表示されます：
   ![サービス ダッシュボード](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-14.png)

3. 提供されたURLをコピーし、ウェブブラウザに貼り付けます。以下のような画面が表示されるはずです：

```text
http://aacbaaa4740274a1a83351e8723871d7-2065184365.us-east-1.elb.amazonaws.com/
```

![APIアプリケーション](/img/projects/devsecops-pipeline-aws/deployment-and-testing/image-15.png)

## 結論

おめでとうございます！このプロジェクトを無事に完了しました。不要な料金を避けるため、Terraform Cloudでリソースを削除することを忘れないでください。これにより、コスト効率を維持し、クリーンな環境を保つことができます。