## 外部ライブラリ「Lombok（ロンボック）」

### lombok.Data
- `import lombok.Data;`: 「Lombok（ロンボック）」という外部ライブラリの機能。
    - クラスに @Data を付けるだけで、Lombokがコンパイル時に以下のコードをすべて自動で書き加える。 ※@Data を使うときは、「デバッグやログが楽になるための準備」をしている。
        - Getter / Setter: すべてのフィールドに対して。
        - toString(): クラス名と全フィールドの中身を表示するメソッド。
        - equals() / hashCode(): オブジェクトの比較用メソッド。
        - RequiredArgsConstructor: final や @NonNull が付いたフィールドを引数に持つコンストラクタ。
    - **注意**
        - **IDEの設定が必要**: VSCodeやIntelliJにプラグインを入れないと、コンパイルエラーに見える。
        - **意図しない挙動**: 全フィールドのSetterが作られるため、値を変えたくない「不変（Immutable）」な設計にしたい時は `@Data` は不適切。
        - **JPA（Entity）との相性**: 
            - `@Data` に含まれる `equals/hashCode/toString` が、Entityのリレーション（循環参照）で無限ループを起こし、アプリが落ちることがある。
            - **対策**: Entityには `@Data` ではなく、必要なものだけ（`@Getter`, `@Setter` など）を個別に付けるのが現場のベストプラクティス。  
          推奨される使い分け
          | 対象 | おすすめのアノテーション |
          | :--- | :--- |
          | **DTO / Form** | `@Data` (全機能) |
          | **Entity (DB)** | `@Getter`, `@Setter`, `@NoArgsConstructor` (個別指定) |
          | **値オブジェクト** | `@Value` (Setterを作らない不変バージョン) |

### toString()
主な目的は **「デバッグとログ」**
- 実装パターン
  - Java 17 以降の最新の書き方（`record`）
  ```Java
  // Java 17+ の record なら何もしなくてOK(手動でオーバーライド)
  public record User(Long id, String name) {}
  // 出力例: User[id=1, name=m_takahisa]
  ```
  - Lombok で自動生成
  ```Java
  @ToString  // @Data または @ToString アノテーションを付ける
  public class User {
      private Long id;
      private String name;
  }
  // 出力例: User(id=1, name=m_takahisa)
  ```
- 特定の項目を「隠す」設定（重要）
```Java
@Data // @Data はデフォルトですべての項目を出力してしまう。（パスワードなどがログに出る）
public class User {
    private Long id;
    private String name;

    @ToString.Exclude // 👈 これで toString() の結果（ログなど）に含まれなくなる
    private String password;
}
```
