# Repository の作成
役割：Javaからデータを保存する。  

## 実装例
```java
package com.m_takahisa.taskapp.repository;

import com.m_takahisa.taskapp.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.Optional;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // メールアドレスでユーザーを探すメソッド（名前からSQLが自動生成されます）
    Optional<User> findByEmail(String email);
}
```
### ポイント
- JpaRepository<User, Long>の継承  
  データベースへの保存・検索・削除の機能が使用可能
- findByEmailメソッド  
  Spring Data JPA の「[メソッドクエリ生成](./java/spring/Spring-Data-JPA/QueryDerivation.md)」という機能
