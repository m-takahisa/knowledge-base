# 「メソッドクエリ生成（Query Derivation）」  
リポジトリに定義した 「メソッドの名前」を Spring が解析して、自動的に SQL（WHERE句）を組み立ててくれる

## メソッドクエリの命名ルール
基本構造は **[導入語] + [プロパティ名] + [比較演算子]** です。

- **導入語**: `findBy`, `readBy`, `getBy`, `queryBy`, `countBy`, `existsBy` など
- **プロパティ名**: エンティティのフィールド名（先頭は大文字にする）
- **比較演算子**: `And`, `Or`, `Between`, `LessThan`, `Like`, `IsNull` など

### 実装例
```java
// SELECT * FROM users WHERE email = ?
User findByEmail(String email);

// SELECT count(*) FROM users WHERE status = ?
long countByStatus(String status);
```

# メソッドクエリのチェックリスト
1. [ ] **単純な 1〜2 条件の検索** → メソッド名で自動生成
2. [ ] **3 条件以上の複雑な検索** → `@Query` (JPQL)
3. [ ] **外部結合 (Fetch Join) が必要** → `@Query` (JPQL)
4. [ ] **DB 固有の関数を使いたい** → `@Query(nativeQuery = true)`

## 逆引き：よく使う比較条件
| キーワード | 生成される SQL (WHERE句) | 用途 |
| :--- | :--- | :--- |
| **(なし)** | `field = ?` | 完全一致 |
| **Containing** | `field LIKE %?%` | 部分一致 |
| **StartsWith** | `field LIKE ?%` | 前方一致 |
| **Between** | `field BETWEEN ? AND ?` | 範囲指定（日付など） |
| **GreaterThan** | `field > ?` | 数値・日付の比較 |
| **In** | `field IN (?, ?, ...)` | リスト内に含まれるか |
| **IsNull** | `field IS NULL` | NULL チェック |
| **True / False** | `field = true / false` | boolean 型の判定 |

## ソートと件数制限: 並び替えとリミット
### 並び替え (OrderBy)
```java
// SELECT * FROM users ORDER BY created_at DESC
List<User> findAllByOrderByCreatedAtDesc();
```

### 件数制限 (Top / First)
```java
// SELECT * FROM users ORDER BY created_at DESC LIMIT 3
List<User> findTop3ByOrderByCreatedAtDesc();
```

## メソッドクエリ運用の注意点
1. **プロパティ名のミス**: 
   フィールド名を間違えると、アプリケーション起動時に `PropertyReferenceException` で落ちます。コンパイルエラーではなく「起動エラー」になるのが要注意ポイント。
2. **メソッド名が長すぎる問題**: 
   `findByNameAndEmailAndStatusAndAgeGreaterThanOrderByCreatedAtDesc` 
   ...ここまで長くなると読めません！条件が3つを超えたら、おとなしく `@Query` を使いましょう。
3. **関連先の参照 (ネスト)**: 
   `findByUser_Name(String name)` のように、アンダースコアを使うことで関連エンティティのフィールドも指定できます（例：PostリポジトリからUserの名前で検索）。
