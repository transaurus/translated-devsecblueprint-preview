---
id: install-config-node-exporter
title: Node Exporter
sidebar_position: 2
---

## 概要

> このインストールは `dsb-node-01` 上で行われます。

[Prometheus' Website]によると、Node ExporterはLinuxシステムのハードウェアおよびOSレベルのメトリクスを監視するための必須ツールです。Prometheusエコシステムの一部であり、CPU使用率、メモリ使用量、ディスクI/Oなどのメトリクスを収集するために広く使用されています。

## インストール手順

1. 必要なディレクトリを作成

まず、`apps`ディレクトリに移動し、Node Exporter用の新しいディレクトリを作成します：

```bash
cd ~/apps
mkdir node-exporter
```

1. Docker Composeファイルを作成

`node-exporter`ディレクトリに移動し、`docker-compose.yml`ファイルを作成します：

```bash
cd ~/apps/node-exporter
touch docker-compose.yml
```

1. Docker Composeファイルを設定

`docker-compose.yml`ファイルを編集し、以下の設定を追加します：

```yaml
version: '3.8'

services:
  node-exporter:
    image: quay.io/prometheus/node-exporter:latest
    container_name: node-exporter
    restart: unless-stopped
    network_mode: "host"
    pid: "host"
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.ignored-mount-points="^/(sys|proc|dev|host|etc)($$|/)"'
    network_mode: host
```

1. Node Exporterをデプロイ

最後に、Docker Composeを使用してNode Exporterをデプロイします：

```bash
docker-compose up -d
```

## 完了

<!-- Sources -->

[Prometheus' Website]: https://prometheus.io/docs/guides/node-exporter/