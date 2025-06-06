---
id: create-repository-pipeline
title: Setting Up Repository And Pipeline
description: A guide to setting up the OWASP Juice Shop project with Sonar Scanning, Gitea, and Jenkins.
sidebar_position: 1
---

## 概要

このガイドのセクションでは、OWASP Juice ShopプロジェクトのJenkinsパイプラインを設定する方法を学びます。バージョン管理にはGitea、静的コード解析にはSonarQube、コンテナ化にはDockerを統合します。このプロセスには、コードベースのクローン、Giteaでのアクセストークンの設定、必要なJenkinsプラグインのインストール、Jenkinsパイプラインの作成、システム間の安全な通信のためのウェブフックとSSHキーの設定が含まれます。このガイドの最後には、コード品質チェック、セキュリティスキャン、デプロイを自動的に処理する完全に自動化されたパイプラインが完成します。

## 前提条件

開始前に、以下を確認してください：

- Giteaインスタンスへのアクセス
- 必要なプラグイン（Git、SonarQube、Dockerなど）がインストールされたJenkins
- SonarQubeインスタンス
- マシンにインストールされたDocker

## ステップ1: コードベースのクローン

ローカルマシンで、juice-shop-sonarscanningリポジトリをクローンします：

```bash
git clone https://github.com/The-DevSec-Blueprint/juice-shop-sonarscanning.git
```

## ステップ2: Giteaで新しいプロジェクトを作成

1. Giteaインスタンスにログインし、新しいリポジトリを作成します。

   ![新しいプロジェクトの作成](/img/projects/devsecops-home-lab/create-configure-pipeline/gitea-create-repository.png)

2. 必要な情報を入力します：

   - **リポジトリ名**: `owasp-juice-shop`
   - **可視性**: `public`
   - **説明**: （オプション）
   - **デフォルトブランチ**: `master`

   ![リポジトリ詳細](/img/projects/devsecops-home-lab/create-configure-pipeline/gitea-repo-details.png)

3. **リポジトリを作成**ボタンをクリックします。

4. リポジトリが正常に作成されたことを確認します。

   ![リポジトリ作成完了](/img/projects/devsecops-home-lab/create-configure-pipeline/gitea-create-project.png)

## ステップ3: ローカルリポジトリをGiteaにポイント

ローカルの`juice-shop-sonarscanning`ディレクトリで、gitのoriginを新しいGiteaリポジトリに更新します：

```bash
cd juice-shop-sonarscanning/
git remote remove origin
git remote add origin http://<your_gitea_server_ip>/<your_username>/owasp-juice-shop.git
git push -u origin master
```

## ステップ4: アプリケーションの認証

コードをプッシュした後、Giteaがアプリケーションの認証を求める場合があります。

![アプリケーションの認証](/img/projects/devsecops-home-lab/create-configure-pipeline/gitea-authorize-application.png)

## ステップ5: 変更の確認

変更がGiteaリポジトリにプッシュされたことを確認します。

![変更確認完了](/img/projects/devsecops-home-lab/create-configure-pipeline/gitea-repo-populated.png)

## ステップ6: GitHubワークフローディレクトリの削除

リポジトリからGitHubワークフローディレクトリを削除します：

```bash
rm -rf .github/workflows
git add .
git commit -m "removing workflows"
git push
```

## ステップ7: CI/CDパイプライン用のJenkinsfileを作成

リポジトリのルートディレクトリに以下の内容で`Jenkinsfile`を作成します：

```groovy
pipeline {
    agent any

    environment {
       SONAR_TOKEN = credentials('sonar-analysis')
       SONAR_PROJECT_KEY = 'owasp-juice-shop'
       DOCKER_IMAGE_NAME = 'owasp-juice-shop'
       NEXUS_DOCKER_REGISTRY = '<your_dsb_hub_ip_address>:8082'
       NEXUS_DOCKER_PUSH_INDEX = '<your_dsb_hub_ip_address>:8083'
       NEXUS_DOCKER_PUSH_PATH = 'repository/docker-host'
    }

    stages {
        stage('Clone') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'Gitea PAT', url: 'http://<your_dsb_hub_ip_address>/<your_username>/owasp-juice-shop.git']])
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }
        stage('Security Scan'){
            parallel {
                stage('Sonar Scan') {
                    steps {
                        script {
                            try{
                                withSonarQubeEnv(installationName: 'Sonar Server', credentialsId: 'sonar-analysis') {
                                    sh '''
                                    docker run --rm \
                                    -e SONAR_HOST_URL="${SONAR_HOST_URL}" \
                                    -e SONAR_TOKEN="${SONAR_TOKEN}" \
                                    -v "$(pwd):/usr/src" \
                                    sonarsource/sonar-scanner-cli \
                                    -Dsonar.projectKey="${SONAR_PROJECT_KEY}" \
                                    -Dsonar.qualitygate.wait=true \
                                    -Dsonar.sources=.
                                    '''
                                }
                            } catch (Exception e) {
                                // Handle the error
                                echo "Quality Qate check has failed: ${e}"
                                currentBuild.result = 'UNSTABLE' // Mark the build as unstable instead of failing
                            }
                        }
                    }
                }
                stage('Security Scan') {
                    steps {
                        sh '''
                            trivy image --severity HIGH,CRITICAL ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }
        stage('Publish') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'nexus', passwordVariable: 'NEXUS_PASSWORD', usernameVariable: 'NEXUS_USERNAME')]) {
                        sh """
                        docker login ${NEXUS_DOCKER_PUSH_INDEX} -u $NEXUS_USERNAME -p $NEXUS_PASSWORD
                        docker tag ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} ${NEXUS_DOCKER_PUSH_INDEX}/${NEXUS_DOCKER_PUSH_PATH}/${DOCKER_IMAGE_NAME}:latest
                        docker push ${NEXUS_DOCKER_PUSH_INDEX}/${NEXUS_DOCKER_PUSH_PATH}/${DOCKER_IMAGE_NAME}:latest
                        """
                    }
                }
            }
        }
        stage('Deploy') {
            agent { label 'dsb-node-01' }
            steps {
                script {
                        echo 'Deploying to DSB Node 01'
                        // Port 3000 is already in use, use 6000 for this application
                        sh '''
                        docker pull ${NEXUS_DOCKER_PUSH_INDEX}/${NEXUS_DOCKER_PUSH_PATH}/${DOCKER_IMAGE_NAME}:latest
                        docker stop ${DOCKER_IMAGE_NAME} || true
                        docker rm ${DOCKER_IMAGE_NAME} || true
                        docker run -d --name ${DOCKER_IMAGE_NAME} -p 8084:3000 ${NEXUS_DOCKER_PUSH_INDEX}/${NEXUS_DOCKER_PUSH_PATH}/${DOCKER_IMAGE_NAME}:latest
                        '''
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
```

このパイプラインは以下のステップを実行します：

1. **リポジトリのクローン**: Giteaリポジトリから`owasp-juice-shop`プロジェクトをプルします。
2. **アプリケーションのビルド**: アプリケーションのDockerイメージをビルドし、ビルド番号でタグ付けします。
3. **セキュリティスキャンの実行**:
   - **SonarQube**: コード品質を分析し、品質ゲートを適用します。
   - **Trivy**: Dockerイメージの脆弱性（HIGHおよびCRITICAL）をスキャンします。
4. **Nexusへの公開**: ビルドされたDockerイメージにタグを付け、Nexus Dockerレジストリにプッシュします。
5. **デプロイ**: Nexusから最新のDockerイメージをプルし、特定のサーバーにデプロイして既存のインスタンスを置き換えます。
6. **クリーンアップ**: ビルド後にワークスペースをクリーンアップします。

これにより、アプリケーションがビルドされ、スキャンされ、レジストリにプッシュされ、安全かつ自動的にデプロイされることが保証されます。

## 結論

これらの手順に従うことで、OWASP Juice ShopプロジェクトをGiteaとJenkins上でSonar Scanningと共にセットアップすることに成功しました。CI/CDパイプラインが準備され、開発プロセス全体を通じてコード品質とセキュリティが確保されるようになりました。