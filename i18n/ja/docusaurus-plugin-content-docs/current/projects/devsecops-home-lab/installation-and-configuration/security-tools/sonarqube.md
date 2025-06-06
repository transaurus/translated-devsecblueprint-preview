---
id: install-config-sonarqube
title: SonarQube
sidebar_position: 4
---

## 概要

> このインストールは `dsb-hub` 上で行われます。

[SonarQubeのWebサイト]によると、SonarQubeはさまざまなプログラミング言語のコード品質を継続的に検査するためのオープンソースプラットフォームです。バグ、セキュリティ脆弱性、コードの臭いを検出するように設計されており、開発者がコードベースの高い基準を維持できるよう詳細なレポートを提供します。SonarQubeは、コードが安全で保守可能であり、業界のベストプラクティスに従っていることを保証するために、DevSecOps環境で広く使用されています。

## 前提条件

1. **PostgreSQLユーザーに切り替える**  
   最初に、データベース関連のタスクを実行するために`postgres`ユーザーに切り替えます:

   ```bash
   sudo -i -u postgres
   ```

2. **SonarQube用のデータベースとユーザーを作成する**  
   SonarQube用の新しいPostgreSQLユーザーとデータベースを作成します:

   ```bash
   createuser sonar
   createdb sonar
   ```

3. **パスワードを設定し権限を付与する**  
   `sonar`ユーザーのパスワードを設定し、必要な権限を付与します:

   ```bash
   psql
   ALTER USER sonar WITH ENCRYPTED PASSWORD 'your_password';
   GRANT ALL PRIVILEGES ON DATABASE sonar TO sonar;

   \c sonar
   GRANT ALL ON SCHEMA public TO sonar;
   GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO sonar;
   GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO sonar;
   GRANT ALL PRIVILEGES ON ALL FUNCTIONS IN SCHEMA public TO sonar;
   GRANT USAGE ON SCHEMA public TO sonar;
   GRANT CREATE ON SCHEMA public TO sonar;
   ```

4. **PostgreSQLセッションを終了し元のユーザーに戻る**  
   PostgreSQLセッションを終了し、元のユーザーに戻ります:

   ```bash
   exit
   exit
   ```

5. **`pg_hba.conf`ファイルを更新する**  
   `pg_hba.conf`ファイルを修正して認証を設定します:

   ```bash
   sudo nano /etc/postgresql/16/main/pg_hba.conf
   ```

   `sonar`ユーザーの`scram-sha-256`認証を有効にするために、次の行を追加します:

   ```conf
   local   sonar           sonar                                   scram-sha-256
   ```

## インストール手順

1. **SonarQubeのダウンロードとインストール**  
   SonarQubeパッケージをダウンロードして展開します:

   ```bash
   cd /opt
   sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.6.0.92116.zip
   sudo apt install unzip -y
   sudo unzip sonarqube-10.6.0.92116.zip
   sudo mv sonarqube-10.6.0.92116 sonarqube
   ```

1. **SonarQubeユーザーの作成**  
   SonarQubeを実行する専用ユーザーを作成し、適切な権限を設定します:

   ```bash
   sudo adduser sonar
   sudo chown -R sonar:sonar /opt/sonarqube
   ```

1. **SonarQubeデータベース設定の更新**  
   `sonar.properties`ファイルを編集して、PostgreSQLデータベースへの接続を設定します:

   ```bash
   sudo nano /opt/sonarqube/conf/sonar.properties
   ```

   PostgreSQL設定を更新します:

   ```ini
   # PostgreSQL設定
   sonar.jdbc.username=sonar
   sonar.jdbc.password=your_password
   sonar.jdbc.url=jdbc:postgresql://localhost/sonar
   ```

1. **SonarQubeサービスのセットアップ**  
   SonarQube用の新しいsystemdサービスファイルを作成します:

   ```bash
   sudo nano /etc/systemd/system/sonarqube.service
   ```

   以下の内容をファイルにコピーします:

   ```ini
   [Unit]
   Description=SonarQube service
   After=syslog.target network.target

   [Service]
   Type=forking
   User=sonar
   Group=sonar

   ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
   ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
   ExecReload=/opt/sonarqube/bin/linux-x86-64/sonar.sh restart

   Restart=on-failure

   [Install]
   WantedBy=multi-user.target
   ```

1. **SystemdのリロードとSonarQubeの起動**  
   systemdデーモンをリロードし、SonarQubeサービスを起動します:

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start sonarqube
   sudo systemctl enable sonarqube
   ```

1. **SonarQubeの動作確認**  
   Webブラウザを開いて以下のURLにアクセスし、SonarQubeが動作していることを確認します:

   ```text
   http://your_ip_address:9000
   ```

   ![SonarQubeインターフェース](/img/projects/devsecops-home-lab/installation-and-configuration/sonarqube-login-dashboard.png)

## SonarQubeの設定

- SonarQubeにログインし、デフォルトの認証情報（ユーザー名: `admin`、パスワード: `admin`）を入力します。

- 初回ログイン後、新しいパスワードに変更します。
  ![alt text](/img/projects/devsecops-home-lab/installation-and-configuration/sonarqube-change-admin-creds.png)

- ダッシュボードに移動します。「プロジェクト作成」をクリックします:
  ![alt text](/img/projects/devsecops-home-lab/installation-and-configuration/sonarqube-dashboard.png)

- ローカルプロジェクトを作成し、表示名とプロジェクトキーに`owasp-juice-shop`を入力します。ブランチは`master`に設定します。
  ![alt text](/img/projects/devsecops-home-lab/installation-and-configuration/sonarqube-project-creation.png)

- 「次へ」をクリックし、「グローバル設定を使用」を選択してから「プロジェクト作成」をクリックします。

## JenkinsとSonarQubeの統合

1. Jenkinsダッシュボードから、**Manage Jenkins > Manage Plugins**に移動し、**SonarQube Scanner**プラグインをインストールします。

2. Jenkinsダッシュボードから、**Credentials > System**に移動します。

3. Systemテーブル内の**Global credentials (unrestricted)**リンクをクリックします。

4. **Add credentials**をクリックし、以下の情報を追加します：

   - **Kind**: Secret Text
   - **Scope**: Global
   - **Secret**: SonarQubeの**User > My Account > Security**でトークンを生成し、ここにコピー＆ペーストします。

5. Jenkinsダッシュボードから、**Manage Jenkins > Configure System**に移動します。

6. **SonarQube Servers**セクションで、**Add SonarQube**をクリックします。以下の情報を追加します：

   - **Name**: SonarQubeインスタンスに一意の名前を付けます。
   - **Server URL**: SonarQubeインスタンスのURL。
   - **Credentials**: ステップ4で作成した認証情報を選択します。

7. **Save**をクリックして統合を完了します。

## 完了です

SonarQubeのインストールと設定、およびJenkinsとの統合が正常に完了しました。このセットアップにより、コード品質とセキュリティ脆弱性を継続的に監視できるようになります。

<!-- Sources -->

[SonarQube's Website]: https://docs.sonarsource.com/sonarqube/latest/#what-is-sonarcloud