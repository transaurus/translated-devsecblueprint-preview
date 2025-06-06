---
id: application-code
title: Application Code - Explained
sidebar_position: 2
---

## 概要

このセクションでは、アプリケーションのコードベースについて詳細に説明します。このプロジェクトは、PythonベースのシンプルなFastAPIアプリケーションで、ローカルで実行したり、コンテナ化してデプロイしたりできます。主な目的は、安全で自動化されたDevSecOpsパイプラインを実演するとともに、テスト用に意図的な脆弱性を組み込んでいます。

## AWSOME-FastAPIの定義

このプロジェクトでは、Dockerコンテナ内にFastAPIアプリケーションをセットアップします。公式のPythonランタイムを使用し、アプリを効率的にデプロイするために必要なすべての設定が含まれています。起動すると、コンテナは自動的にFastAPIアプリを実行し、ポート80で公開します。

このプロジェクトの目的は、意図的にいくつかの脆弱性を含んだ状態でDevSecOpsパイプラインを通じて展開することです。詳細については、[main.py](https://github.com/The-DevSec-Blueprint/awsome-fastapi/blob/main/main.py)ファイルのコードを確認してください。

### 要件

- **Docker**: アプリケーションのコンテナ化と実行用
- **Python 3.12+**: 最新の安定版で、最新機能との互換性を確保
- **FastAPI**: API構築用フレームワーク
- **Uvicorn**: アプリケーション実行用のASGIサーバー

### 特徴

- **コンテナ化されたアプリケーション**: コンテナを使用したデプロイを簡素化
- **Python 3.12.5 ランタイム**: 最新の機能とセキュリティパッチとの互換性を確保
- **最適化された依存関係インストール**: `requirements.txt`を活用した効率的なパッケージ管理

### プロジェクト構成

```bash
awesome-fastapi/
├── Dockerfile         # Configuration for the Docker container
├── requirements.txt   # Python dependencies
├── main.py            # Entry point for the FastAPI app (contains sample vulnerabilities)
└── ...
```

### セットアップとインストール

#### 1. リポジトリのクローン

以下のコマンドでプロジェクトリポジトリをクローンします：

```bash
git clone https://github.com/your-username/awesome-fastapi.git
cd awesome-fastapi
```

#### 2. Dockerイメージのビルド

プロジェクトのルートディレクトリで以下のコマンドを実行し、Dockerイメージをビルドします：

```bash
docker build -t awesome-fastapi .
```

#### 3. Dockerコンテナの実行

イメージのビルド後、コンテナを起動します：

```bash
docker run -d -p 80:80 awesome-fastapi
```

このコマンドにより、FastAPIアプリがlocalhostのポート80で起動します。

#### 4. アプリケーションへのアクセス

コンテナが実行中になったら、ブラウザでアプリケーションにアクセスできます：

```text
http://localhost:80
```

### 依存関係

アプリケーションは、`requirements.txt`ファイルで指定された以下のPythonパッケージに依存しています：

- `fastapi`: API構築用の主要フレームワーク
- `uvicorn`: アプリケーション実行用のASGIサーバー

これらの依存関係をローカルにインストールするには、以下を実行します：

```bash
pip install -r requirements.txt
```

### 注意事項

- FastAPIアプリケーションのデフォルトエントリーポイントは`main.py`で、アプリケーションインスタンスは`app`という名前です。設定が異なる場合は、Dockerfileの`CMD`ディレクティブを適宜更新してください。
- デフォルトでは、コンテナはポート80でアプリケーションを公開します。別のポートを使用する場合は、Dockerfileの`EXPOSE`および`CMD`ディレクティブを必要に応じて変更してください。

このシンプルなセットアップにより、最小限の手間でFastAPIアプリケーションを実行、テスト、デプロイできるとともに、安全なDevSecOpsパイプラインに統合できます。