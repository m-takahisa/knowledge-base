# @Query による JPQL の記述

## 特徴
- データベースの種類（MySQL, PostgreSQLなど）に依存しない。
- エンティティのフィールド名で記述できる。

## 実装例
java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    // 引数を :name のようにバインドして使用する
    @Query("SELECT u FROM User u WHERE u.name = :name AND u.status = 'ACTIVE'")
    List<User> findActiveUsersByName(@Param("name") String name);
}
