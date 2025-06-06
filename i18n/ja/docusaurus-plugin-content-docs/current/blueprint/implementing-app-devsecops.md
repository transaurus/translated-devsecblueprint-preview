---
id: implementing-app-devsecops-pipelines
title: Building Application DevSecOps Pipelines (In Theory)
description: Building DevSecOps Pipelines In Theory
sidebar_position: 5
---

著者: [Damien Burks]

## はじめに

常に心に留めておいてほしいことがあります。適切に設計されたDevSecOpsパイプラインは、Secure Software Development Life Cycle（SSDLC）に従います。セキュリティを独立した最終ステップ（SDLC）として扱うのではなく、DevSecOpsパイプラインでは、開発、テスト、デプロイのあらゆる側面にセキュリティを組み込んでいます。

このセクションでは、DevSecOpsパイプラインが持つべき重要なステージを概説します。これらはアプリケーションデプロイメントの場合に焦点を当てています。

## DevSecOpsパイプラインのステージ

私は他のソリューションと比較してJenkinsを使用することを決めました。なぜなら、私が最も慣れ親しんでいるからです。個人的には、JenkinsfileはGitLab RunnerやGitHub/Gitea Actionsのファイルよりも読みやすいと考えています。そのため、Jenkinsfileの書き方に関するこのドキュメントを読むことを強くお勧めします: [Using A Jenkinsfile](https://www.jenkins.io/doc/book/pipeline/jenkinsfile/)

通常、DevSecOpsのための**Jenkinsパイプライン**には、アプリケーションをビルド、テスト、スキャン、デプロイするためのさまざまなステージが含まれます。以下にリストしたアウトラインは擬似コードで、各ステージに簡単な説明が続きます:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                // Checking out code from Git repository (like GitHub, Gitea, GitLab, etc.)
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Example: Building the application
                // Could also include containerizing the application as well
            }
        }

        stage('Run Application Tests') {
            // Example: Running unit tests, acceptance tests, integration tests
            // It's usually best to do these in parallel to save time on builds :)
            parallel {
                stage('Unit Test'){
                    steps {
                    }
                }
                stage('Acceptance Test'){
                    steps {
                    }
                }
                stage('Integration Test'){
                    steps {
                    }
                }
            }
        }

        stage('Security Scanning') {
            parallel {
                stage('SAST Scanning'){
                    steps {
                        // This stage will run SAST Scanning tests against your code base.
                    }
                }
                stage('DAST Scanning'){
                    steps {
                        // This stage will run DAST scans against your running application.
                        // You'll want to ensure you run your app locally and run the solution against it.
                    }
                }
                stage('Container Scanning'){
                    steps {
                        // This stage would be dependant whether or not you're deploying
                        // a container to Kubernetes or Docker Swarm (or just plain old Docker).
                    }
                }
                stage('Dependency Scanning') {
                    steps {
                        // This stage will check your dependencies of you applicatino and
                        // log any vulnerabilities.
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                // Lastly, we will deploy your application. This could to any
                // environment you want.
            }
        }
    }

    post {
        always {
            // Example: Archiving reports and test results
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            junit '**/target/test-*.xml'

            // Best practice - clean up the workspaces after it's all said an done.
            // Don't be that guy...
            cleanWs()
        }
    }
}
```

## CI/CDパイプラインに統合するセキュリティツール

Jenkins以外にどのCI/CDおよびセキュリティツールを使用すべきか悩んでいる場合、またはCI/CDについてより詳細に理解したい場合、以下のリソースを用意しています。

| **Tool Name**                                                             | **Description**                                                                                             | **Category**               |
| ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- | -------------------------- |
| [SonarQube](https://www.sonarqube.org/)                                   | One of the most popular SAST tools, SonarQube analyzes code quality and security across multiple languages. | SAST                       |
| [Semgrep](https://semgrep.dev/)                                           | A fast and flexible static analysis tool for identifying security vulnerabilities in source code.           | SAST                       |
| [Bandit](https://bandit.readthedocs.io/en/latest/)                        | Popular Python-focused SAST tool for identifying security issues in Python codebases.                       | SAST                       |
| [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/) | A widely-used tool that checks for known vulnerabilities in project dependencies.                           | SAST                       |
| [OWASP ZAP](https://owasp.org/www-project-zap/)                           | One of the most popular DAST tools, ZAP identifies security vulnerabilities in running web applications.    | DAST                       |
| [Arachni](https://www.arachni-scanner.com/)                               | A modular DAST scanner that is widely used for testing modern web applications.                             | DAST                       |
| [Nikto](https://github.com/sullo/nikto)                                   | A highly popular DAST tool that scans web servers for vulnerabilities and misconfigurations.                | DAST                       |
| [Trivy](https://github.com/aquasecurity/trivy)                            | A comprehensive vulnerability scanner for containers and cloud-native applications.                         | Container & Cloud Security |
| [Anchore Engine](https://github.com/anchore/anchore-engine)               | Open-source tool for scanning container images for vulnerabilities and enforcing security policies.         | Container Security         |
| [Clair](https://github.com/quay/clair)                                    | A popular open-source tool for scanning vulnerabilities in Docker and OCI container images.                 | Container Security         |

## 追加リソース

パイプライン構築に役立つさらに多くのツールについて学ぶには、Marek Šottlが作成したこの素晴らしいリソースをチェックしてください: [Ultimate DevSecOps Library](https://github.com/sottlmarek/DevSecOps?tab=readme-ov-file#ultimate-devsecops-library)

### 書籍

| **Book Name**                                                                                                                 | **Author**                                              | **Link**                          |
| ----------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- | --------------------------------- |
| Continuous Delivery with Docker and Jenkins - Second Edition: Create secure applications by building complete CI/CD pipelines | Rafal Leszko                                            | [Amazon](https://amzn.to/3Oaw7M0) |
| Using Docker: Developing and Deploying Software with Containers                                                               | Adrian Mouat                                            | [Amazon](https://amzn.to/3UVl2SS) |
| Agile Application Security: Enabling Security in a Continuous Delivery Pipeline                                               | Laura Bell, Michael Brunton-Spall, Rich Smith, Jim Bird | [Amazon](https://amzn.to/3AKNwrx) |

<!-- Links -->

[Damien Burks]: https://www.youtube.com/@damienjburks