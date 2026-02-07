# @Modifying アノテーション
- **役割**: `@Query` で定義したクエリが「データの変更（INSERT, UPDATE, DELETE）」を伴うものであることを JPA に通知します。
- **必須条件**: `@Modifying` を使うメソッドには、通常サービス層（Serviceクラス）で `@Transactional` を付与する必要があります。
## 実装例
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    @Modifying
    @Query("UPDATE User u SET u.status = :status WHERE u.id = :id")
    int updateStatus(@Param("id") Long id, @Param("status") String status);
}
```
### @Modifying 使用時のチェックリスト
1. [ ] **@Modifying** を付けたか？（ないと実行時エラー）
2. [ ] **@Transactional** をサービス層に付けたか？（ないとロールバック不可）
3. [ ] **clearAutomatically = true** は必要か？（同じメソッド内でその後も Entity を使うなら必須）

# コンテキストの整合性：clearAutomatically プロパティ
`@Modifying` で DB を直接更新しても、**Java のメモリ上にある Entity（永続性コンテキスト）は古いまま** という現象が起きます。
## 概要
- `clearAutomatically = true` を設定すると、クエリ実行後に JPA のキャッシュ（永続性コンテキスト）を強制的にクリアします。
- これにより、次にデータを取得する際、DB から最新の状態を再読み込みするようになります。
## 実装例
```java
@Modifying(clearAutomatically = true)
@Query("UPDATE User u SET u.lastLogin = CURRENT_TIMESTAMP WHERE u.id = :id")
void updateLastLogin(@Param("id") Long id);
```

# 更新処理の選び方
1. **基本：Entityを操作する**:
   - `findById` で取得 → setterで値を変更 → （トランザクション終了時に自動保存）。
   - **メリット**: JPA のライフサイクル（監査用ログなど）が正しく動く。

2. **@Modifying + @Query を使う場合**:
   - 「全ユーザーのステータスを一括で変更する」といった **バルク演算（大量更新）** の場合。
   - **メリット**: 1件ずつループして `save()` するより圧倒的に高速。
