# 学習ログ
Spring Boot 4.xは仮想スレッドの標準化とAI関連の強化  

## Springの構造イメージ
| 項目 | 説明 | 例え（車） |
| :--- | :--- | :--- |
| Spring | プロジェクトすべての総称（エコシステム）| 「メーカーブランド名（シリーズ名）」 |
| Spring Framework | DI（依存性の注入）やAOP、トランザクション管理など、すべての基礎を提供するライブラリ| 核となる「エンジン」 |
| Spring Boot | Spring Frameworkを簡単に使い始めるための「自動設定機能」を備えたフレームワーク | 「タイヤ」や「ハンドル」 |
| Spring Data / Security / Cloud など | DB接続やセキュリティなど、特定の機能に特化した拡張パック。| 「ナビシステムとか？」 |

## Springの主要なプロジェクト
- Spring Boot
- Spring MVC
- Spring Security
- Spring Data

## DI（Dependency Injection：依存性の注入）
### 「特定の誰か」を指定する
1. 道具（Repository）を用意
2. 作業員（Service）に道具（Repository）を使うための「鍵」を渡す
   - final: 道具にロック（final）をかける
   - @RequiredArgsConstructor（Lombok）: finalがついたフィールドを引数に持つコンストラクタを自動生成（鍵の受け渡し）
3. 受付窓口（Controller）に作業員（Service）を呼び出す「鍵（暗証番号）」を渡す
   - @RequiredArgsConstructor（Lombok）
### 「インターフェース（合鍵）」を用意する（インターフェースで疎結合）
1. 「役割（合鍵の型）」を作る
2. 「道具（実装クラス）」を2つ作る（テスト用、本番用など）
3. 作業員（Service）は「合鍵」で受け取る（interfaceを継承したRepository）
4. 道具の切り替えは`@Primary`か`application.yml`でProfile機能を使う
   - 本番用に`@Profile("jp")`を設定
   - ymlにspring.profiles.active=jpで本番の道具を使用

## AOP（Aspect Oriented Programming：アスペクト指向プログラミング）
## 仮想スレッド
## Spring Data JPA とデータベース
