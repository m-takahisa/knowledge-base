# @Queryアノテーション:  
`JpaRepository` の命名ルールだけでは対応できない複雑な検索や、パフォーマンスチューニングが必要な時に登場
## 特徴
- データベースの種類（MySQL, PostgreSQLなど）に依存しない。
- エンティティのフィールド名で記述できる。
## 実装例
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    // 引数を :name のようにバインドして使用する
    @Query("SELECT u FROM User u WHERE u.name = :name AND u.status = 'ACTIVE'")
    List<User> findActiveUsersByName(@Param("name") String name);
}
```

# @Query による ネイティブ SQL の記述
## 特徴
- `nativeQuery = true` を設定する。
- データベースの「テーブル名」や「カラム名」を直接記述する。
- データの更新（UPDATE）や削除（DELETE） を行う場合は [@Modifying](./java/spring/Spring-Data-JPA/@Modifying.md) が必要
## 実装例
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    @Query(value = "SELECT * FROM users WHERE last_login < DATE_SUB(NOW(), INTERVAL 1 MONTH)", 
           nativeQuery = true)
    List<User> findInactiveUsers();
}
```

# Fetch Join によるパフォーマンス最適化
## 概要
- `JOIN FETCH` を使うことで、LAZY設定にしている関連データも一括でロードする。
- N+1問題をスマートに解決できる。
## 実装例
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    // Userと一緒に、その投稿リスト(posts)も1回のSQLで取得する
    @Query("SELECT u FROM User u JOIN FETCH u.posts WHERE u.id = :id")
    Optional<User> findUserWithPostsById(@Param("id") Long id);
}
```

# 現場での判断基準 💡@Query の使い分け
1. **命名ルール (`findBy...`)**: 
   - 条件が2つ以内、かつ単純な比較の場合。
2. **JPQL (`@Query`)**: 
   - 命名ルールではメソッド名が長くなりすぎる場合。
   - **Fetch Join** を使って N+1 問題を解決したい場合。
3. **ネイティブ SQL**: 
   - DB固有の機能（全文検索や複雑な日付計算など）が必要な場合。
   - 速度を極限まで追求する場合。
