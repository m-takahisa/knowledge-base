# Spring vs Spring Boot 比較チートシート

Spring Framework と Spring Boot の根本的な役割の違いと、Boot がもたらした恩恵についてのまとめです。

---

## 1. 概念の比較表

| 項目 | Spring Framework | Spring Boot |
| :--- | :--- | :--- |
| **主要な目的** | 疎結合なJavaアプリを構築するための基盤 | Springアプリを迅速に起動・実行するためのツール |
| **設定の負担** | XMLやJavaConfigで一つずつ設定が必要 (重い) | **Auto Configuration** により設定を自動化 (軽い) |
| **サーバー** | Tomcatなどを外部に用意してデプロイ | **内蔵サーバー** により単体で起動可能 |
| **依存関係管理** | ライブラリとそのバージョンを個別に管理 | **Starter** により関連ライブラリを一括管理 |
| **学習コスト** | 設定が複雑で、覚えるべきことが多い | 規約に従えば、少ない学習量で動かし始められる |

---

## 2. Spring Boot の 3大特徴

### ① 自動設定 (Auto Configuration)
クラスパス上のライブラリを検知し、適切な設定を自動的に適用します。
- 例：`spring-boot-starter-data-jpa` があれば、DB接続設定を自動で試みる。

### ② スターター (Starter Dependencies)
「やりたいこと」に合わせてライブラリのセットを提供します。
- `spring-boot-starter-web`: REST APIやMVC開発に必要なセット。
- `spring-boot-starter-test`: JUnitやMockitoなどのテスト用セット。

### ③ 内蔵サーバー (Embedded Server)
アプリ自体にTomcatやJettyが組み込まれているため、環境構築の手間が省けます。
- `java -jar` コマンドひとつで本番環境でも動作可能。

---

## 3. どちらを使うべきか？
- **新規開発**: 100% **Spring Boot** を推奨。
- **既存保守**: 古いプロジェクトでは Spring Framework 単体の設定知識が必要になる場合がある。

> **m_takahisa へのメモ**:
> Spring Boot を使っていても、その中身は Spring Framework の DI（依存性の注入）などの仕組みで動いています。Boot はあくまで「Springを使いやすくする強力なパッケージ」と捉えればOKです。
