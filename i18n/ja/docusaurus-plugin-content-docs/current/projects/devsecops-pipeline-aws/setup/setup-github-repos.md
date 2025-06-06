---
id: setup-github-repos
title: Setting Up GitHub Repositories
sidebar_position: 1
---

## 概要

このガイドでは、GitHubリポジトリを個人アカウントにセットアップする手順を説明します。GitHubに慣れていない方のために説明すると、GitHubはGitというバージョン管理システムを利用したウェブベースのプラットフォームで、開発者がコードの変更を管理・追跡するのに役立ちます。また、プロジェクトの共同作業を容易にし、リビジョンを追跡し、世界中どこからでもコードの貢献を可能にします。詳細については、[GeeksforGeeksのGitHub入門記事](https://www.geeksforgeeks.org/introduction-to-github/)を参照してください。さらに、GitHubは無料アカウントを提供しており、これは常に嬉しい特典です！

## 前提条件

始める前に、GitHubアカウントを持っていることを確認してください。まだ持っていない場合は、[GitHubアカウント作成ガイド](https://docs.github.com/ja/get-started/start-your-journey/creating-an-account-on-github)に従って作成してください。

## 設定手順

### リポジトリのフォーク

まず、リポジトリを個人のGitHubアカウントにフォークする必要があります：

1. GitHubアカウントにログインします。
2. 最初のプロジェクトのランディングページに移動します：[AWS DevSecOps Pipeline](https://github.com/The-DevSec-Blueprint/aws-devsecops-pipeline)。
3. 右上隅の**Fork**ボタンをクリックします。
4. **Owner**として個人アカウントを選択し、**Create Fork**をクリックします。**Copy the main branch only**オプションが有効になっていることを確認してください。

   ![alt text](/img/projects/devsecops-pipeline-aws/setup/image-8.png)

5. 上記の手順を2つ目のプロジェクトでも繰り返します：[Awesome FastAPI](https://github.com/The-DevSec-Blueprint/awsome-fastapi)。
6. 以下のコマンドを使用して、両方のリポジトリをローカルマシンにクローンします（例）：

   ```text
   git clone https://github.com/The-DevSec-Blueprint/awsome-fastapi.git
   ```