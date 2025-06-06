---
id: devsecops-fundamentals
title: DevSecOps Fundamentals
description: Defining and explaining DevSecOps at a high-level
sidebar_position: 4
---

著者: [Damien Burks]

OK！あなたはこのブループリントの私のお気に入りのセクションに到達しました - _DevSecOpsとは何か？_

この時点で、以下の概念について基本的な理解を持っているはずです：

1. DevOps
1. バージョン管理システム
1. プログラミング
1. Linux/Bashスクリプティング
1. CI/CD
1. アプリケーションセキュリティ
1. セキュアソフトウェア開発ライフサイクル（SSDLC）...およびソフトウェア開発ライフサイクル（SDLC）

もし理解しているなら、先に進んで構いません。もし理解していないなら、**強く**戻って読み、実験し、プロジェクトやツールを試すことをお勧めします。

## 概要

[RedHat](https://www.redhat.com/en/topics/devops/what-is-devsecops)によると、_DevSecOpsは開発（development）、セキュリティ（security）、運用（operations）を意味します。これは、セキュリティをITライフサイクル全体を通じて共有責任として統合する、文化、自動化、プラットフォーム設計へのアプローチです。_

これの素晴らしい点は、DevOpsの原則（協力、自動化、継続的デリバリー）を拡張し、セキュリティをソフトウェア開発ライフサイクル（SDLC）のすべてのフェーズに組み込むことです。これについては後ほど説明します。

最終的な目標は、セキュリティをSDLCの「左」、つまりより早い段階にシフトさせ、脆弱性が重大な問題になる前に特定および緩和されるようにすることです。

![DevSecOpsモデル](/img/blueprint/devsecops_model.png)

> 画像出典: [Atlassian | DevSecOpsツール](https://www.atlassian.com/devops/devops-tools/devsecops-tools)

時が経つにつれ、DevSecOpsはDevOpsの限界に対する応答として進化してきました。DevOpsではセキュリティが後付けになることが多かったためです。DevSecOpsは、DevOpsのアジャイルおよび継続的デリバリーのプラクティスにセキュリティを含める必要性から生まれ、組織がセキュリティ脆弱性のリスクを減らし、業界規制へのコンプライアンスを確保できるようにします。

### なぜ重要なのか？

従来のセキュリティプラクティスは、DevOpsの高速な世界においてボトルネックを生み出すことがよくあります。なぜなら、それらは通常開発サイクルの終わりに行われるからです。DevSecOpsはこれを解決し、セキュリティ対策を最初から組み込むことで、より速く、より安全なソフトウェアリリースを可能にします。

## DevSecOpsのコア原則

DevSecOps全体をよりよく理解するためには、DevSecOpsのコア原則を理解する必要があります。以下に理解すべき**4つ**の主要な原則を挙げます：

1. **セキュリティの統合**

   セキュリティはSDLCのすべてのフェーズおよびサブフェーズに統合されます。実際、SSDLCはDevSecOpsの前身です。この包括的なアプローチにより、**セキュリティが後付けではない**、開発プロセスの基本的な側面であることが保証されます。

2. **自動化**

   自動化はDevSecOpsにおいて重要であり、セキュリティチェックが開発者エクスペリエンスに_**深刻な**_影響を与えずに一貫して適用されるようにします。開発者エクスペリエンスへの影響を最小限に抑えることに特に重点を置いています。さらに、静的コード分析や脆弱性スキャンなどの自動化されたセキュリティテストをCI/CDパイプラインに統合することで、開発ライフサイクルの早い段階で問題を検出できます。

3. **協力**

   DevSecOpsは、開発、セキュリティ、運用チーム間の協力文化を促進します。この文化的な動きはサイロを打破し、開発者、運用、セキュリティチーム間の共有責任を奨励し、安全なソフトウェア開発とデプロイのための非常に結束力のある効果的な戦略の作成につながります。

4. **継続的なフィードバックと監視**

   継続的なフィードバックループと監視により、アプリケーションとインフラのセキュリティ状況に関するリアルタイムの洞察が得られます。これにより、チームはセキュリティ問題が発生した際に迅速に特定し、修復することができます。

## 追加リソース

次のセクションに進む前に、認定資格、読むべき書籍、YouTube動画など、私がお勧めするさまざまなリソースをいくつか紹介します：

### 書籍

| **Book Name**                                                                                                           | **Author**                                            | **Link**                          |
| ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- | --------------------------------- |
| The Phoenix Project                                                                                                     | Gene Kim, Kevin Behr, and George Spafford             | [Amazon](https://a.co/d/7emFNLg)  |
| Continuous Delivery                                                                                                     | Jez Humble and David Farley                           | [Amazon](https://a.co/d/0ixNvOq)  |
| The DevOps Handbook                                                                                                     | Gene Kim, Patrick Debois, John Willis, and Jez Humble | [Amazon](https://a.co/d/3NLl4hM)  |
| Securing DevOps                                                                                                         | Julien Vehent                                         | [Amazon](https://a.co/d/6Fgtzin)  |
| DevSecOps: A leader’s guide to producing secure software without compromising flow, feedback and continuous improvement | Glenn Wilson                                          | [Amazon](https://amzn.to/4fsfMye) |
| Cloud Native DevOps with Kubernetes                                                                                     | John Arundel and Justin Domingus                      | [Amazon](https://a.co/d/2fGdXaE)  |
| Infrastructure as Code                                                                                                  | Kief Morris                                           | [Amazon](https://a.co/d/cINH2dd)  |
| Kubernetes Security                                                                                                     | Liz Rice                                              | [Amazon](https://a.co/d/2kLIXF9)  |
| Securing DevOps                                                                                                         | Julien Vehent                                         | [Amazon](https://a.co/d/6Fgtzin)  |

### 記事

- https://developer.ibm.com/articles/devsecops-what-and-why/
- https://www.microsoft.com/en-us/security/business/security-101/what-is-devsecops
- https://www.redhat.com/en/topics/devops/what-is-devsecops
- https://aws.amazon.com/compliance/shared-responsibility-model/

### 認定資格

DevSecOpsへの移行を目指す方におすすめの認定資格を、特に順番はありませんが以下にリストアップします：

| Name                                                                                                                           | Level        |
| ------------------------------------------------------------------------------------------------------------------------------ | ------------ |
| [CompTIA Security+](https://www.comptia.org/certifications/security)                                                           | Beginner     |
| [CompTIA Linux+](https://www.comptia.org/certifications/linux)                                                                 | Beginner     |
| [Certified Kubernetes Administrator (CKA)](https://www.cncf.io/certification/cka/)                                             | Intermediate |
| [Certified Kubernetes Application Developer (CKAD)](https://www.cncf.io/certification/ckad/)                                   | Intermediate |
| [Certified DevSecOps Professional (CDP)](https://www.practical-devsecops.com/certifications/certified-devsecops-professional/) | Intermediate |
| [Certified DevSecOps Expert (CDE)](https://www.practical-devsecops.com/certifications/certified-devsecops-expert/)             | Expert       |
| [ISC^2 CSSLP](https://www.isc2.org/Certifications/CSSLP)                                                                       | Expert       |

クラウドに特化したい場合は、以下の資格を検討すると良いでしょう：

| **Name**                                                                                                                            | **Level**    |
| ----------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| [AWS Certified Security – Specialty](https://aws.amazon.com/certification/certified-security-specialty/)                            | Intermediate |
| [Microsoft Certified: Azure Security Engineer Associate](https://learn.microsoft.com/en-us/certifications/azure-security-engineer/) | Intermediate |
| [Google Professional Cloud Security Engineer](https://cloud.google.com/certification/cloud-security-engineer)                       | Intermediate |

### YouTube動画

#### DevSecOpsとは？8分で解説

<iframe
  width="100%"
  height="500"
  src="https://www.youtube.com/embed/nrhxNNH5lt0?si=OC_5Tq6pBROq7DyC"
  frameborder="0"
  allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen
></iframe>

#### DevSecOpsとは？7分で解説

<iframe
  width="100%"
  height="500"
  src="https://www.youtube.com/embed/VLEF6MU0Wfg?si=dYktpIAnAej9Z2A7"
  frameborder="0"
  allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen
></iframe>

#### DevSecOpsの旅を加速：5分で学ぶ5つの重要なスキル

<iframe
  width="100%"
  height="500"
  src="https://www.youtube.com/embed/7J9rjMbPZm4?si=FuH6jox0BE57Ip-n"
  frameborder="0"
  allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen
></iframe>

#### DevSecOpsとは？ - Hackitect's playground

<iframe
    width="100%"
    height="400"
    src="https://www.youtube.com/embed/DOlE7691Q3o?si=oBUjzHIQawYkvTZJ"
    frameborder="0"
    allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen
></iframe>

<!-- Links -->

[Damien Burks]: https://www.youtube.com/@damienjburks