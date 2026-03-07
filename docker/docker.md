# Docker化
Dockerを導入すると、DB（PostgreSQL）のインストールや設定に悩まされることなく、コマンド一つでどこでも同じ環境が作れるようになる

## Docker化に必要なもの
1. Dockerfile (アプリ用)
2. docker-compose.yml (全体管理用)
3. application.yml の修正

## 導入の手順
1. 上記ファイルをプロジェクトに追加
2. `mvn clean package` でJARファイルを生成
3. ターミナルで `docker-compose up -d` を実行
