# エンティティの作成
Entity（エンティティ）の作成は、Javaのオブジェクトとデータベースのテーブルを結びつける。  
## ポイント💡
- `@Column(nullable = false)`: DB側でもNOT NULL制約をかけることで、データの整合性を担保。
- `LocalDate`/`LocalDateTime`: 古い Date クラスではなく、Java 8以降のモダンな日時APIを使用。
- `@PrePersist` / `@PreUpdate`: 「いつ作られたか」をプログラム側で自動管理する仕組み。
- `import lombok.Data;`: 「Lombok（ロンボック）」という外部ライブラリの機能。
    - クラスに @Data を付けるだけで、Lombokがコンパイル時に以下のコードをすべて自動で書き加える。
    - @Data を使うときは、「デバッグやログが楽になるための準備」をし
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
## 実装例
```Java
package com.m_takahisa.taskapp.entity;

import jakarta.persistence.*;
import lombok.Data;
import java.time.LocalDate;
import java.time.LocalDateTime;

@Entity
@Table(name = "tasks")
@Data
public class Task {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // Userとの「多対一」のリレーション設定
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @Column(nullable = false, length = 100)
    private String title;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Column(nullable = false)
    private String status = "TODO"; // 初期値

    @Column(name = "due_date")
    private LocalDate dueDate;

    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @Column(name = "updated_at", nullable = false)
    private LocalDateTime updatedAt;

    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
        updatedAt = LocalDateTime.now();
    }

    @PreUpdate
    protected void onUpdate() {
        updatedAt = LocalDateTime.now();
    }
}
```
