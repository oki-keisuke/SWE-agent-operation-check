SWE-agentの動作確認

## 1. 概要

「 SWE-agent 」は、GitHub上のイシュー（機能追加や不具合の報告）を自動的に解決する「 AIソフトウェアエンジニア 」です。
ベンチマークテスト SWE-bench に含まれる 12.47% のイシューを解決できます。
このリポジトリでは，SWE-agentの動作確認のため，docker-composeとシェルコマンドを用いてSWE-agentの環境構築を行う．
▼SWE-agent
https://github.com/princeton-nlp/SWE-agent

## 2. 必要な環境

- Docker
- Docker Compose
- Git Bash（Windows OSの場合）
- ターミナル (Mac OSの場合)

## 3. セットアップ手順

### 3.1 リポジトリのクローン

まず，このリポジトリをローカルマシンにクローンします．
作業したいディレクトリにcdして以下のコマンドをGit bashで実行します．

```sh
git clone https://github.com/princeton-nlp/SWE-agent.git
cd SWE-agent
```

### 3.2 Dockerfileの確認

以下の内容のDockerfileを確認します。

```Dockerfile
FROM python:3.9

# Set the working directory
WORKDIR /app

# Install nodejs
RUN apt update && \
    apt install -y nodejs npm && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install Docker CLI using the official Docker installation script
RUN curl -fsSL https://get.docker.com -o get-docker.sh && \
    sh get-docker.sh

# Copy the application code
COPY . /app

# Install Python dependencies
RUN pip install -e '.'

# Install react dependencies ahead of time
RUN cd sweagent/frontend && npm install

# Set the entrypoint
ENTRYPOINT ["tail", "-f", "/dev/null"]
```

## 3.3 docker-compose.ymlの作成

SWE-agentディレクトリ内にdocker-compose.ymlを作成します．以下の内容にします．

```yml
version: '3.8'
services:
  swe-agent:
    build: .
    container_name: swe-agent
    volumes:
      - ./output:/app/output
```

## 3.4 起動スクリプトの作成

SWE-agentディレクトリ内にrun.shを作成し，以下のコマンドを入力します．

```sh
#!/bin/sh

echo "Running docker-compose up --build..."
docker-compose up --build
```

## 3.5 コンテナのビルドと起動

Git bashを起動し，SWE-agentディレクトリに移動したのち，以下のコマンドを入力します．

```sh
sh run.sh
```
