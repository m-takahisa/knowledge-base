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
- ログの設定を一か所でまとめて設定できて、処理を監視することでより安全に運用が可能
- 特定のメソッドの実行時間を測って「遅い処理」の確認、メモリ消費量（ざっくり）の確認も可能
### 準備
1. 依存関係の確認(build.gradle)
   - 未リリースで実験的なSpring Boot 4.0.4ではエラーになる。下記で解消。  
      - Spring Boot 4.0.4 → 3.4.1, Gradleバージョン（9.4.0）→Gradleバージョン（8.10.2）  
   - 文字化け。下記で解消。
      - 実行/デバック構成＞VMオプションに`-Dfile.encoding=COMPAT`を設定。
2. アスペクト（外付け処理）を作成

## 仮想スレッド
- スレッドとは 「CPU（コンピュータの頭脳）が一度に実行する『作業の列』」 のこと
### 仮想スレッドのイメージ
- 今まではOSにいちいち頼んで紙媒体でもらっていたものを、電子化されて自身でいくつでも自由にメモが作れるようになったイメージ
- スペースには限りがあるが、効率的に活用しているイメージ。ただし処理が重いものは通常スレッドがよい。

## Spring Data JPA とデータベース
