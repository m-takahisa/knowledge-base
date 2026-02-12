# Controller と Service の役割分担

| 項目 | Controller (受付・窓口) | Service (実務・ロジック) |
| :--- | :--- | :--- |
| **主な責務** | HTTPリクエストの制御 | ビジネスロジック（業務ルール）の実行 |
| **データの入口** | ブラウザからのリクエスト (@RequestBody等) | Controllerからの引数（DTO等） |
| **主な処理** | パスやメソッドの振り分け、バリデーション | 計算、データの加工、複数Repositoryの集約 |
| **トランザクション** | 基本的には関与しない | `@Transactional` でデータの一貫性を守る |
| **データの出口** | 画面（HTML）や JSON の返却 | 処理結果（EntityやDTO）の返却 |

## 💡 m_takahisa メモ
- **Controllerは「何をするか」を決める場所**
- **Serviceは「どうやるか」を具体的に実現する場所**

# 綺麗な連携のための 3 つのルール

## 1. Controller を「薄く」保つ
Controllerの中に `if` 文による複雑な分岐や計算を書かない。
「Serviceを呼ぶだけ」のコードが理想的です。

## 2. Service は「再利用性」を意識する
特定のURLに依存した処理（`HttpServletRequest` を直接触るなど）をServiceに書かない。
純粋な Java オブジェクトを受け取り、純粋な結果を返すようにします。

## 3. データの受け渡しは DTO を活用する
Entity（DBの形）をそのまま Controller まで持ち出さず、
画面表示専用のクラス（DTO）に詰め替えて受け渡しをすることで、DB変更の影響を抑えられます。
