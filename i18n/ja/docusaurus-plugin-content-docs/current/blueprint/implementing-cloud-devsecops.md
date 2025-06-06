---
id: implementing-cloud-devsecops-pipelines
title: Building Cloud DevSecOps Pipelines (In Theory)
description: Building DevSecOps Pipelines In Theory
sidebar_position: 8
---

著者: [Damien Burks]

## クラウドDevSecOpsパイプラインのステージ（Terraformデプロイメント）

この例では、**JenkinsパイプラインとTerraform**を使用して、クラウドインフラストラクチャをコード（IaC）としてプロビジョニングおよび管理しています。Jenkinsパイプラインには、Terraform設定のリントと検証、セキュリティ問題のスキャン、インフラストラクチャの適用、およびデプロイ後のテストを行うステージが含まれています。

以下に示すアウトラインは疑似コードであり、各ステージごとに簡単な説明が続きます：

```groovy
pipeline {
    agent any

    environment {
        AWS_CREDENTIALS = credentials('aws-access-key-id')  // Example for AWS IAM credentials
        TF_VERSION = '1.0.11'                              // Terraform version
        TERRAFORM_DIR = 'terraform'                        // Terraform directory
        SNYK_ORG_NAME = "snyk-org-id"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checking out code from version control system (GitHub, GitLab, Gitea, etc.)
                checkout scm
            }
        }

        stage('Terraform Init') {
            steps {
                // Initializing Terraform in the specified directory
                sh """
                terraform -version
                cd ${TERRAFORM_DIR}
                terraform init
                """
            }
        }

        stage('Terraform Lint') {
            steps {
                // Running terraform fmt to check for formatting issues
                sh """
                cd ${TERRAFORM_DIR}
                terraform fmt -check
                """
            }
        }

        stage('Terraform Validate') {
            steps {
                // Validating the Terraform configuration
                sh """
                cd ${TERRAFORM_DIR}
                terraform validate
                """
            }
        }

        stage('Terraform Plan') {
            steps {
                // Running terraform plan to check the changes that will be applied
                sh """
                cd ${TERRAFORM_DIR}
                terraform plan -out=tfplan
                """
            }
        }

        stage('Security Scanning') {
            parallel {
                stage('Checkov Scan') {
                    steps {
                        // Scanning the Terraform configuration for misconfigurations
                        sh """
                        cd ${TERRAFORM_DIR}
                        checkov --directory . --quiet
                        """
                    }
                }
                stage('Snyk Scan') {
                    steps {
                        // Running Snyk to check security issues in the Terraform configurations and generate a report
                        sh """
                        cd ${TERRAFORM_DIR}
                        snyk iac test --severity-threshold=high --org=${SNYK_ORG_NAME} --report
                        """
                    }
                }
            }
        }

        stage('Terraform Apply') {
            when {
                expression {
                    return params.APPLY_TERRAFORM == true  // Optional parameter to conditionally apply
                }
            }
            steps {
                // Applying the Terraform plan to the cloud provider (e.g., AWS, GCP, Azure)
                sh """
                cd ${TERRAFORM_DIR}
                terraform apply -auto-approve tfplan
                """
            }
        }

        stage('Post-Deployment Testing') {
            steps {
                // Run integration tests or security tests after infrastructure deployment if you're like to. This
                // can look completely different for everyone's use case to ensure your infrastructure is alive.
                sh """
                cd ${TERRAFORM_DIR}/tests
                ./run-post-deployment-tests.sh
                """
            }
        }
    }

    post {
        always {
            // Example: Archiving Terraform logs and test results
            archiveArtifacts artifacts: '**/terraform.tfstate', allowEmptyArchive: true
            junit '**/test-results/*.xml'

            // Clean up workspace after build
            cleanWs()
        }
    }
}
```

## その他のInfrastructure as Code（IaC）言語

Terraform以外の言語（ただしTerraformが最良ですが）を使用してクラウドにインフラストラクチャをデプロイする方法を探している場合は、以下の表を参照してください：

| **Tool / Language**                                                                          | **Short Summary**                                                                                               |
| :------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------- |
| **[Terraform](https://www.terraform.io/)**                                                   | Multi-cloud provisioning and management using HashiCorp Configuration Language (HCL).                           |
| **[AWS CloudFormation](https://aws.amazon.com/cloudformation/)**                             | Native AWS IaC tool using YAML/JSON templates for declarative resource provisioning in AWS.                     |
| **[Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/)** | Azure-native JSON templates for deploying and configuring Azure resources.                                      |
| **[Bicep](https://docs.microsoft.com/azure/azure-resource-manager/bicep/)**                  | A higher-level DSL for Azure resource deployment that simplifies ARM templates.                                 |
| **[Google Cloud Deployment Manager](https://cloud.google.com/deployment-manager)**           | Declarative IaC for GCP resources using YAML, Jinja2, or Python templates.                                      |
| **[Pulumi](https://www.pulumi.com/)**                                                        | Provision infrastructure across multiple clouds using general-purpose languages (TypeScript, Python, Go, etc.). |

## クラウドCI/CDパイプラインに統合するセキュリティツール

パイプライン（またはローカル環境）で使用すべきクラウドセキュリティツールについて検討している場合、以下にリソースのリストをまとめました：

| **Tool Name**                                                             | **Description**                                                                                               | **Category**                                                      |
| ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| [SonarQube](https://www.sonarqube.org/)                                   | One of the most popular SAST tools, SonarQube analyzes code quality and security across multiple languages.   | SAST                                                              |
| [Semgrep](https://semgrep.dev/)                                           | A fast and flexible static analysis tool for identifying security vulnerabilities in source code.             | SAST                                                              |
| [Bandit](https://bandit.readthedocs.io/en/latest/)                        | Popular Python-focused SAST tool for identifying security issues in Python codebases.                         | SAST                                                              |
| [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/) | A widely-used tool that checks for known vulnerabilities in project dependencies.                             | SAST                                                              |
| [Snyk](https://snyk.io/)                                                  | A developer-first tool that scans for vulnerabilities in code, open-source dependencies, containers, and IaC. | SAST, Dependency Scanning, Container Security, and Cloud Security |
| [OWASP ZAP](https://owasp.org/www-project-zap/)                           | One of the most popular DAST tools, ZAP identifies security vulnerabilities in running web applications.      | DAST                                                              |
| [Arachni](https://www.arachni-scanner.com/)                               | A modular DAST scanner that is widely used for testing modern web applications.                               | DAST                                                              |
| [Nikto](https://github.com/sullo/nikto)                                   | A highly popular DAST tool that scans web servers for vulnerabilities and misconfigurations.                  | DAST                                                              |
| [Trivy](https://github.com/aquasecurity/trivy)                            | A comprehensive vulnerability scanner for containers and cloud-native applications.                           | Container & Cloud Security                                        |
| [Anchore Engine](https://github.com/anchore/anchore-engine)               | Open-source tool for scanning container images for vulnerabilities and enforcing security policies.           | Container Security                                                |
| [Clair](https://github.com/quay/clair)                                    | A popular open-source tool for scanning vulnerabilities in Docker and OCI container images.                   | Container Security                                                |

## 追加リソース

パイプライン構築を支援するためのより多くのツールについて学ぶには、Marek Šottlが作成したこの素晴らしいリソースをチェックしてください：[Ultimate DevSecOps Library](https://github.com/sottlmarek/DevSecOps?tab=readme-ov-file#ultimate-devsecops-library)

<!-- Links -->

[Damien Burks]: https://www.youtube.com/@damienjburks