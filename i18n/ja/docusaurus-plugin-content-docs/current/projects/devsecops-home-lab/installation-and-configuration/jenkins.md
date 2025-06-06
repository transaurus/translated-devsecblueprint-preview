---
id: install-config-jenkins
title: Jenkins
sidebar_position: 3
---

## 概要

> このインストールは `dsb-hub` 上で行われます。

[Jenkins ユーザードキュメント]によると、Jenkinsは自己完結型のオープンソース自動化サーバーであり、ソフトウェアのビルド、テスト、配布またはデプロイに関連するあらゆるタスクを自動化するために使用できます。CI/CDプラットフォームです。

## 前提条件

1. **Javaのインストール**

Jenkinsをインストールする前に、システムにJavaがインストールされていることを確認してください:

- パッケージマネージャーを更新し、Javaをインストールします:

  ```bash
  sudo apt update
  sudo apt install fontconfig openjdk-17-jre
  ```

## インストール手順

1. **パッケージマネージャーの設定とJenkinsのインストール**

- Jenkinsをパッケージマネージャーに追加するために、Jenkinsキーをダウンロードしてインストールします:

  ```bash
  sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
  ```

- Jenkinsリポジトリをソースリストに追加します:

  ```bash
  echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
  ```

- パッケージリストを更新し、Jenkinsをインストールします:

  ```bash
  sudo apt-get update
  sudo apt-get install jenkins
  ```

1. **Jenkinsの有効化と起動**

- 起動時にJenkinsが自動で開始されるように設定します:

  ```bash
  sudo systemctl enable jenkins
  ```

- Jenkinsを起動します:

  ```bash
  sudo systemctl start jenkins
  ```

1. **Jenkinsの動作確認**

- Jenkinsがアクティブであることを確認するためにステータスをチェックします:

  ```bash
  sudo systemctl status jenkins
  ```

- Jenkinsが正常に起動している場合、以下のような出力が表示されます:

  ```bash
  ● jenkins.service - Jenkins Continuous Integration Server
     Loaded: loaded (/usr/lib/systemd/system/jenkins.service; enabled; preset: enabled)
     Active: active (running) since [DATE]; [TIME] ago
     Main PID: 9188 (java)
  ```

## 設定手順

1. **WebブラウザからJenkinsにアクセス**

- Webブラウザを開き、以下にアクセスします:

  ```bash
  http://your_ip:8080
  ```

- Jenkinsのセットアップ画面が表示されます。

  ![Jenkins Setup](/img/projects/devsecops-home-lab/installation-and-configuration/jenkins-init-config.png)

1. **初期管理者パスワードの取得**

- セットアップを進めるには、初期管理者パスワードが必要です。サーバー上で以下のコマンドを実行して取得します:

  ```bash
  sudo cat /var/lib/jenkins/secrets/initialAdminPassword
  ```

- パスワードをコピーし、Webインターフェースのパスワードボックスに入力します。

  ![Initial Admin Password](/img/projects/devsecops-home-lab/installation-and-configuration/jenkins-init-password.png)

1. **推奨プラグインのインストール**

- 管理者パスワードを入力した後、**Install suggested plugins**をクリックし、Jenkinsが必要なプラグインをインストールするのを待ちます。

  ![Install Plugins](/img/projects/devsecops-home-lab/installation-and-configuration/jenkins-install-suggested-plugins.png)

1. **管理者アカウントの設定**

- プラグインのインストールが完了すると、管理者アカウントの設定を求められます。IPの詳細を入力し、Jenkinsインスタンスを設定します。

  ![Admin Setup](/img/projects/devsecops-home-lab/installation-and-configuration/jenkins-admin-user.png)

1. **インスタンスIPの設定**

- 管理者情報を入力した後、インスタンスのURL IPアドレスを設定するか、「Not Now」を押します。

  ![インスタンスの設定](/img/projects/devsecops-home-lab/installation-and-configuration/jenkins-instance-config.png)

1. **ホームページに移動**

- これでホームページが表示されます。

  ![ホームページ](/img/projects/devsecops-home-lab/installation-and-configuration/jenkins-home.png)

## Jenkinsノードのインストール

> このセクションでは、`dsb-node-01`にJenkinsノードをインストールおよび設定する方法を説明します。

1. **Jenkinsユーザーの作成**

- Jenkinsユーザーを作成し、適切な権限を設定します：

  ```bash
  sudo adduser jenkins
  sudo chown -R jenkins:jenkins /home/jenkins
  sudo chmod 755 /home/jenkins
  sudo usermod -aG docker jenkins
  ```

1. **Javaのインストール**

- JenkinsノードにJavaをインストールします：

  ```bash
  sudo apt install openjdk-17-jre-headless
  ```

1. **Jenkins UIでのノード設定**

- Jenkins UIで、**ダッシュボード > Jenkinsの管理 > ノード**に移動します。「新規ノード」をクリックし、「永続エージェント」を選択して「OK」を押します。

  ![ノード設定](/img/projects/devsecops-home-lab/installation-and-configuration/jenkins-create-node.png)

- エージェントの設定で、「リモートルートディレクトリ」を`/home/jenkins/agent`に設定し、設定を保存します。

1. **エージェントのセキュリティ設定**

- Jenkins UIで、**ダッシュボード > Jenkinsの管理 > セキュリティ**に移動し、「インバウンドエージェントのTCPポート」セクションまでスクロールします。「ランダム」を選択し、適用/保存します。

  ![セキュリティ設定](/img/projects/devsecops-home-lab/installation-and-configuration/jenkins-node-conf-security.png)

1. **Jenkinsノードのセットアップ**

- `dsb-node-01`で、エージェントディレクトリを作成します：

  ```bash
  cd /home/jenkins && mkdir agent
  ```

- JenkinsエージェントのJARファイルをダウンロードします：

  ```bash
  wget http://<jenkins-server-url>/jnlpJars/agent.jar
  ```

- Jenkinsエージェントを実行します：

  ```bash
  java -jar agent.jar -jnlpUrl http://<jenkins-server-url>/computer/<node-name>/slave-agent.jnlp -secret <secret-key> -workDir "/home/jenkins/agent"
  ```

> **注**: シークレットキーはJenkinsノード設定ページで確認できます。

![シークレットキーの場所](/img/projects/devsecops-home-lab/installation-and-configuration/jenkins-node-cmd.png)

1. **Jenkinsエージェント用のSystemdサービスの作成**

- Jenkinsエージェント用の新しいsystemdサービスを作成します：

  ```bash
  sudo nano /etc/systemd/system/jenkins-agent.service
  ```

- サービスファイルに以下の設定を追加します：

  ```ini
  [Unit]
  Description=Jenkins Agent

  [Service]
  User=jenkins
  Group=jenkins
  ExecStart=/usr/bin/java -jar /home/jenkins/agent/agent.jar -jnlpUrl http://<jenkins-server-url>/computer/<node-name>/slave-agent.jnlp -secret <secret-key> -workDir /home/jenkins/agent
  Restart=always

  [Install]
  WantedBy=multi-user.target
  ```

- systemdをリロードし、Jenkinsエージェントサービスを開始します：

  ```bash
  sudo systemctl daemon-reload
  sudo systemctl start jenkins-agent
  sudo systemctl enable jenkins-agent
  ```

## 完了です

これでJenkinsのセットアップが完了しました！

<!-- Sources -->

[Jenkinsユーザードキュメント]: https://www.jenkins.io/doc/#what-is-jenkins