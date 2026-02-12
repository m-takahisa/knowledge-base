# Spring Boot: @Controller / @RestController の役割

| 項目 | 内容・責務 |
| :--- | :--- |
| **主な役割** | HTTPリクエストの受け付けと、レスポンスの返却。 |
| **ルーティング** | URL（パス）とメソッドを紐付ける（@RequestMappingなど）。 |
| **入力チェック** | リクエストパラメータやJSONのバリデーション（@Valid）。 |
| **型変換** | 文字列で届くリクエストデータをJavaオブジェクト（DTO）に変換。 |
| **処理の委譲** | 実際の業務ロジックは `@Service` に丸投げする。 |
| **表示の制御** | 遷移先の画面名（HTML）を返す、またはJSONデータを返す。 |

## 💡メモ
Controllerに「ビジネスロジック（計算やDBの複雑な操作）」は書かないのが鉄則！
Controllerが太ると、テストがしにくく、再利用しにくいコードになってしまいます。

# Controller レイヤーの主要アノテーション

| アノテーション | 役割 |
| :--- | :--- |
| **@Controller** | 画面（HTML）を返すクラスに付与。 |
| **@RestController** | JSONやXMLなどを返す（API用）クラスに付与。 |
| **@GetMapping** | データの取得リクエスト（SELECT系）をマッピング。 |
| **@PostMapping** | データの登録リクエスト（INSERT系）をマッピング。 |
| **@PathVariable** | URLに含まれる値（/users/{id}など）を取得。 |
| **@RequestBody** | 送られてきたJSONをJavaオブジェクトに変換。 |
