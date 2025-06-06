---
id: what-is-application-security
title: What is Application Security?
description: Application Security Defined
sidebar_position: 2
---

著者: [Damien Burks]

The DevSec Blueprintの最初のページへようこそ。このコンテンツを理解しやすい順序で構成するよう最善を尽くしました。DevSecOpsの概念について掘り下げる前に、まずアプリケーションセキュリティから始めるべきいくつかの重要な概念について説明します。

## 概要

アプリケーションセキュリティとは何でしょうか？[Imperva](https://www.imperva.com/learn/application-security/application-security/)によると、アプリケーションセキュリティはソフトウェアアプリケーションのコードとデータをサイバーセキュリティの脅威や脆弱性から保護することを目的としています。これには、コードをスキャンするツールの使用、コード内の問題の修正、アプリケーション設計時の脅威モデリングなどが含まれ、コードに脆弱性が混入するのを防ぎます。

このプロセスは実際には「セキュアソフトウェア開発ライフサイクル（SSDLC）」の各フェーズで行われます。SSDLCの詳細は次のページで定義と説明を行いますが、まずその重要性について少しお話ししましょう。

## アプリケーションセキュリティの重要性

私たちは情報が売買されるデジタル時代に生きています。理解しておくべきことは、これらの情報のほとんどが何らかのアプリケーションの背後や内部に保存されているということです。例えば、Webアプリケーションにログインして請求書の支払いをしたり、銀行口座を確認したりするでしょう？では、そのデータを表示したり支払いを行ったりするアプリケーションに脆弱性があったらどうなるか想像してみてください。その時点で、誰かがシステムを侵害し、あなたの機密情報や他の人の情報を盗み、それを悪用したりダークウェブで売却したりする可能性があります。

つまり、アプリケーションは攻撃者にとって脆弱性を悪用して不正アクセス、データ窃取、その他の悪意のある目的を達成するための標的になりやすいのです。アプリケーションの構造が不適切だったり、脅威モデリングやセキュリティ対策が不十分だった場合、以下のような影響や結果が生じる可能性があります:

- **データ侵害**: 機密データの喪失または暴露
- **コンプライアンス違反**: GDPRやHIPAAなどの業界規制への非準拠
- **評判の毀損**: 顧客の信頼や市場での地位の喪失

## 一般的なアプリケーションセキュリティの脆弱性

一般的なセキュリティ脆弱性のリストは毎年変化します。[OWASP Top 10](https://owasp.org/www-project-top-ten/)に注目することが重要です。これは組織に大きな影響を与える最も一般的な脆弱性を把握するための優れた方法です。

しかし、私が経験した中で今も大きな影響力を持つ最も一般的な脆弱性をいくつか挙げておきます:

1. **SQLインジェクション**: 悪意のある入力により、攻撃者がデータベースへのクエリを操作し、機密情報にアクセスする可能性があります。
2. **クロスサイトスクリプティング（XSS）**: 攻撃者が悪意のあるスクリプトをWebページに注入し、他のユーザーに影響を与える可能性があります。
3. **不適切な認証と認可**: 弱いまたは不適切に設定されたアクセス制御により、未承認のユーザーが機密データにアクセスする可能性があります。
4. **機密データの露出**: 暗号化やトークン化などの機密情報の保護が不十分だと、データ侵害につながる可能性があります。

## SASTとDAST

これらの脆弱性を軽減または排除する方法として、2つの主要なテスト手法があります: **静的アプリケーションセキュリティテスト（SAST）**と**動的アプリケーションセキュリティテスト（DAST）**です。これらの技術は、コードや実行中のアプリケーション内の脆弱性を特定する上で重要な役割を果たします。

**静的アプリケーションセキュリティテスト（SAST）**は、アプリケーションのソースコード、バイトコード、またはコンパイルされたバイナリを脆弱性について分析するホワイトボックステスト技術です。ホワイトボックステストとは、テスト前にシステムを理解している状態で脆弱性をテストすることを指します。

SASTツールはプログラムを実行せずにコードをスキャンするため、開発者は開発プロセスの早い段階で潜在的なセキュリティ問題を発見できます。SASTの利点は以下の通りです:

- **早期検出**: SASTはソースコード上で動作するため、コードがコンパイルまたはデプロイされる前の開発段階でセキュリティ問題を検出できます。
- **包括的なカバレッジ**: SASTはすべてのコードパスを分析し、SQLインジェクション、XSS、バッファオーバーフロー、安全でないコーディングプラクティスなどの脆弱性を発見できます。
- **自動化**: SASTツールはCI/CDパイプラインに統合可能で、各コミットやビルド時に自動的にコードを分析します。

一方、**Dynamic Application Security Testing (DAST)** はブラックボックステスト手法で、アプリケーションの稼働状態を実際の攻撃をシミュレートして検査します。ブラックボックステストとは、テスターがアプリケーションや製品について事前知識を持たない状態で行うテストのことで、多くのペンテスターやハッカーが実際に行う方法です。

DASTツールはアプリケーションをその動作環境（例：Webアプリ、API）でテストし、ランタイム時にのみ明らかになる脆弱性を発見します。これにより、アプリのどのコンポーネントが実際に脆弱であるかを非常に正確に評価できます。DASTの利点は以下の通りです：

- **ランタイム検出**: DASTは動作中のアプリケーションをテストするため、設定ミス、認証問題など、アプリケーション使用時に発生する欠陥を捕捉できます。
- **ソースコード不要**: DASTはアプリケーションのコードへのアクセスを必要としないため、サードパーティ製アプリケーションやAPIの脆弱性特定に有効です。
- **実世界シミュレーション**: DASTは実際の攻撃者の挙動を模倣し、外部視点からの潜在的な攻撃経路を特定します。

SASTがコード自体の脆弱性発見に焦点を当てるのに対し、DASTはデプロイ後のアプリケーションパフォーマンスを評価します。両者を組み合わせることで包括的なセキュリティテストが可能です：

- **SAST**: 早期段階での検出に最適で、静的コードをスキャンしアプリケーションがデプロイされる前に問題を捕捉します。
- **DAST**: 設定ミスやランタイム固有の問題など、動作環境でしか見つからない脆弱性の特定に不可欠です。

SASTとDASTの両方をSDLCに組み込むことで、コードレベルと運用レベルの両方でセキュリティテストが実施され、脆弱性が見過ごされるリスクを最小限に抑えられます。

## ツールとプロジェクト

試すべきツールは数多く存在します。これらの中にはアプリケーションペネトレーションテストスキルを真に試し、これらの脆弱性がどのように機能するかをより深く理解するのに役立つものもあります。以下の表にそれらをまとめました：

| **Project Name**                                                                      | **Description**                                                                                                           |
| ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)                         | A vulnerable web app for practicing web security testing with a focus on OWASP Top 10 vulnerabilities.                    |
| [Damn Vulnerable Web Application (DVWA)](https://github.com/digininja/DVWA)           | PHP/MySQL-based web app designed for practicing penetration testing skills.                                               |
| [Vulnerable Node.js Application (VulnNode)](https://github.com/cr0hn/vulnerable-node) | A vulnerable Node.js application for practicing JavaScript/Node.js specific security testing.                             |
| [Hackazon](https://github.com/rapid7/hackazon)                                        | An e-commerce platform that simulates a real-world site with multiple security vulnerabilities.                           |
| [Mutillidae II](https://github.com/webpwnized/mutillidae)                             | A free, open-source vulnerable web application that covers over 40 vulnerabilities for testing.                           |
| [bWAPP](http://www.itsecgames.com/)                                                   | A PHP-based vulnerable app with over 100 web vulnerabilities, covering OWASP Top 10 and beyond.                           |
| [OWASP WebGoat](https://owasp.org/www-project-webgoat/)                               | An insecure web application designed for practicing web security vulnerabilities and solutions.                           |
| [NodeGoat](https://github.com/OWASP/NodeGoat)                                         | A deliberately insecure Node.js app, focusing on vulnerabilities specific to JavaScript and Node.js.                      |
| [OWASP Security Shepherd](https://owasp.org/www-project-security-shepherd/)           | A platform designed for learning security principles with a capture-the-flag format for both beginner and advanced users. |

## 追加リソース

アプリケーションセキュリティをより深く理解するために、私が厳選した以下のリソースを参照してください：

### 書籍

| **Book Title**                                                               | **Author**                     | **Link**                          |
| ---------------------------------------------------------------------------- | ------------------------------ | --------------------------------- |
| Alice and Bob Learn Application Security                                     | Tanya Janca                    | [Amazon](https://a.co/d/awZU6Mb)  |
| The Web Application Hacker's Handbook: Finding and Exploiting Security Flaws | Dafydd Stuttard & Marcus Pinto | [Amazon](https://amzn.to/4fJwQPU) |
| Hacking APIs: Breaking Web Application Programming Interfaces                | Corey J. Ball                  | [Amazon](https://amzn.to/4fOGHnK) |

### YouTube動画

#### アプリケーションセキュリティ101 - 8分で知るべきこと

<iframe
  width="100%"
  height="500"
  src="https://www.youtube.com/embed/Dp019cWu1cg?si=AHNFdEYNPoa4XGWZ"
  frameborder="0"
  allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen
></iframe>

#### SASTとDASTとは？

<iframe
  width="100%"
  height="500"
  src="https://www.youtube.com/embed/Nz7WCh9HQpo?si=jlloCTA87MRipc0b"
  frameborder="0"
  allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen
></iframe>

### 記事

ブログを好み、アプリケーションセキュリティについてさらに深く掘り下げたい場合は、以下の記事をチェックしてください：

- https://www.crowdstrike.com/en-us/cybersecurity-101/application-security/
- https://www.ibm.com/topics/application-security
- https://medium.com/googledeveloperseurope/what-is-application-security-all-you-need-to-know-guide-blog-3ceee69deb11

<!-- Links -->

[Damien Burks]: https://www.youtube.com/@damienjburks