# Java 8 to 17/21 差異まとめチートシート

Java 8からモダンJava（17/21）への移行で、実務上特によく使う変更点をまとめています。

## 1. 構文の進化（より短く、安全に）

### Record (Java 14+)
DTOや値オブジェクトの定義を劇的に簡略化します。
- `Getter`, `equals`, `hashCode`, `toString` を自動生成。
- フィールドは `final` になり、不変（Immutable）を保証。

```java
// Java 17/21
public record UserResponse(Long id, String name) {}
```

### Text Blocks (Java 13+)
SQLやJSONを改行を含めてそのまま書けます。エスケープ（`\"`）地獄からの解放。
```java
String query = """
    SELECT id, name, email
    FROM users
    WHERE status = 'ACTIVE'
    ORDER BY created_at DESC
    """;
```

### Switch式の拡張 (Java 14+)
値を返すことができ、`break` 漏れによるバグを防げます。
```java
String result = switch (day) {
    case MONDAY, FRIDAY -> "仕事";
    case SATURDAY, SUNDAY -> "休み";
    default -> {
        String message = "不明な日";
        yield message; // 複数行の場合はyieldで返す
    }
};
```

### instanceof のパターンマッチング (Java 16+)
キャストが不要
```java
if (obj instanceof String s) {
    System.out.println(s.toLowerCase()); // すでにStringとして扱える
}
```

## 2. Java 21 最大の目玉：Virtual Threads
スレッドの概念が根本から変わりました。
- 従来のThread: OSのスレッドと1対1。大量に作るとメモリ不足になる。
- Virtual Thread: JVMが管理する軽量スレッド。数百万個作っても大丈夫。
- メリット: Spring BootなどのWebアプリで、同時リクエスト処理能力が飛躍的に向上。
```java
// 仮想スレッドの作成例
Thread.ofVirtual().start(() -> {
    System.out.println("Running in virtual thread");
});
```

## 3. 標準APIの改善
便利メソッドの追加
```java
// List.of (Java 9): 不変リストの作成
List<String> list = List.of("A", "B", "C");

// var (Java 10): ローカル変数の型推論
var userService = new UserService();

// String.isBlank() (Java 11): 空白文字のみの判定
"   ".isBlank(); // true
```

## 4. 実行環境・パフォーマンス
- **デフォルトGC**: `Parallel GC` (8) → `G1GC` (17以降標準)。
- **ZGC (Java 15+)**: テラバイト級のメモリでも停止時間を10ms以下に抑える次世代GC。
- **Spring Boot 3 対応**: Spring Boot 3以降は **Java 17以上が必須**。
