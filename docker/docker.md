# Docker化
Dockerを導入すると、DB（PostgreSQL）のインストールや設定に悩まされることなく、コマンド一つでどこでも同じ環境が作れるようになる。  
`Docker Desktop`が必要。
1. 「ソースコード」から環境構築：別のPCでの開発
2. 「イメージ」から環境構築：テスト担当者用、本番環境へのデプロイ
→ローカルで共有
→リモートで共有

## 「ソースコード」から環境構築
### 実行手順
1. ソースコードのコピー
   ```bash
   git clone https://github.com/ユーザーID/リポジトリ名.git
   ```
  - `git clone`を使用しない場合の、Docker化に必要な資材

    | 分類 | ファイル / フォルダ名 | 役割 |
    | :--- | :--- | :--- |
    | Docker設定 | docker-compose.yml<br> Dockerfile | 複数コンテナの管理図<br>アプリのビルド・実行手順書 |
    | ソースコード | src/ | JavaプログラムやHTML本体 |
    | ビルド設定 | build.gradle.kts<br>settings.gradle.kts<br>gradlew, gradle/ | Gradleの依存関係定義<br>プロジェクト構成定義<br>Gradle実行用のラッパー（Java未インストール対策） |
    | アプリ設定 | src/main/resources/application.yaml | ポート番号やDB接続情報 |
  
    ※build/ フォルダや .gradle/ フォルダなどは、実行時に自動生成されるためコピー不要

2. フォルダに移動
   ```bash
   cd リポジトリ名
   ```

3. Docker起動
   ```bash
   docker-compose up --build
   ```

### 動作確認  
起動ログに `Started TaskApplication` と出たら、そのPCのブラウザで以下を確認
1. アプリ: `http://localhost:8085`
2. DB管理 (pgAdmin): `http://localhost:8080`

## 「イメージ」から環境構築（ローカルでの共有）
1. 送信側手順
  1. イメージの作成（ビルド）
     ```bash
     # Dockerfile に基づいてアプリのイメージが作成される
     docker compose build app
     ```
  2. イメージファイルの書き出し
     ```bash
     # イメージ名を取得
     docker images
     
     # Dockerfile に基づいてアプリのイメージが作成される
     docker save -o my-task-app.tar my-task-app-app:latest
     ```
2. 受信側手順
  1. `.tar`(アプリ本体（ビルド済み）)と`docker-compose.yml`(DB（PostgreSQL）や pgAdmin との連携設定)を任意のフォルダに配置
  2. イメージの読み込み
     ```bash
     # イメージが登録される
     docker load -i my-task-app.tar
     ```
  3. 起動
     ```bash
     docker compose up -d
     ```

## 「イメージ」から環境構築（リモート（Docker Hub）での共有）  
受信側は`docker-compose.yml`のみで構築可能。
Docker Hub の管理画面でリポジトリの Visibility を Private に設定可能。  
1. 送信側手順
   1. Docker Hub へログイン
      ```bash
      docker login
      ```
    2. イメージへの「タグ」付け
       ```bash
       # イメージ名の形式: [ユーザーID]/[イメージ名]:[タグ]
       docker tag my-task-app-app:latest mtakahisa/task-app:v1.0
       ```
    3. イメージのアップロード（Push）
       ```bash
       # イメージ名の形式: [ユーザーID]/[イメージ名]:[タグ]
       docker push mtakahisa/task-app:v1.0
       ```
2. 受信側手順
   1. docker-compose.yml の書き換え
      app サービスの部分を、ビルド（build: .）ではなく、Push したイメージを直接使うように修正  
      ```java
      services:
        app:
          # 修正前:
          # build: .
          # 修正後: Docker Hub のイメージを指定
          image: mtakahisa/task-app:v1.0
          (以下省略)
      ```
    2. 起動（`docker-compose.yml`配置場所で）
       ```bash
       docker compose up -d
       ```




## ビルド済みの「イメージ」だけを共有して実行させる方法
### 導入の手順
1. 上記ファイルをプロジェクトに追加
2. `mvn clean package` でJARファイルを生成
3. ターミナルで `docker-compose up -d` を実行


