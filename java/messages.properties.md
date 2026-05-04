記載例：
```
# フィールド名の日本語定義
username=ユーザー名
email=メールアドレス
password=パスワード
# タスクステータス
todo=未完了
doing=進行中
done=完了
# ユーザー登録関連
user.register.email.already_exists=このメールアドレスは既に登録されています
```

■使い方
- バックエンド (Back-end)：**""で囲む**
```
  // 例外の種類に応じたメッセージキーを決定
  String messageKey = "user.register.generic_error";
  if (e instanceof UserException.AlreadyExistsException) {
      messageKey = "user.register.email.already_exists";
  }
```

- フロントエンド (Front-end)：**#{...} 構文**
```
<select th:field="*{status}" class="form-select" id="status">
    <option value="TODO" th:text="#{todo}">未完了</option>
    <option value="DOING" th:text="#{doing}">進行中</option>
    <option value="DONE" th:text="#{done}">完了</option>
</select>
```
