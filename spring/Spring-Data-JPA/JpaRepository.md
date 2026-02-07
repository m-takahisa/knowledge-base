# JpaRepository とは

## 概要
- Spring Data JPA の中心となるインターフェース。
- これを継承したインターフェースを作成するだけで、基本的な CRUD（作成・読込・更新・削除）機能が自動で実装される。

## 定義方法
java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // これだけで基本的なメソッド（save, findById, delete 等）が使えるようになる
}

- **第一引数 (`User`)**: 操作対象のエンティティクラス。
- **第二引数 (`Long`)**: そのエンティティの主キー（@Id）の型。

# 継承構造の役割

- **CrudRepository**: 基本的な CRUD 操作（save, find, delete）を提供。
- **PagingAndSortingRepository**: ページング処理やソート機能を提供。
- **JpaRepository**: 上記すべてに加え、JPA 固有の操作（一括削除、キャッシュのフラッシュ等）を提供。

# クエリメソッドの例

Repository 内にメソッドを宣言するだけで、Spring が名前を解析して SQL を作ってくれます。

- **findBy...**: `List<User> findByName(String name);`
  → `SELECT * FROM users WHERE name = ?`
- **...And...**: `User findByEmailAndPassword(String email, String password);`
  → `WHERE email = ? AND password = ?`
- **...Containing**: `List<User> findByNameContaining(String keyword);`
  → `WHERE name LIKE %keyword%`
- **...OrderBy...**: `List<User> findAllByOrderByCreatedAtDesc();`
  → `ORDER BY created_at DESC`

# JpaRepository 運用のコツ

1. **複雑なクエリは @Query を使う**: [@Query詳細](./java/java8-to-21-differences.md)
   メソッド名が長くなりすぎる（例：`findByNameAndEmailAndStatusOrderByCreatedAtDesc`）場合は、無理に命名ルールに従わず、JPQL を直接書くほうが可読性が高いです。
2. **デフォルトの save メソッド**:
   `save()` は「IDがなければ INSERT」「IDがあれば UPDATE」を自動で判定します。
3. **戻り値は Optional を活用**:
   `findById(id)` は `Optional<T>` を返すので、`orElseThrow()` などを使ってスマートにエラーハンドリングを行うのが現代的な書き方です。
