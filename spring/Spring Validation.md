**「フィールドエラー」 と 「オブジェクト（グローバル）エラー」 の扱いの違い**

■状況
<img width="800" height="300" alt="image" src="https://github.com/user-attachments/assets/e1b11557-0581-493b-bdb0-e8af804606ef" />

■入力欄の下に表示されているエラー（フィールドエラー）
- @NotBlank や @Size などのアノテーションをフィールドに直接付けた場合に発生
- Thymeleafでの制御: th:errors="*{title}" のように、特定の項目名を指定して呼び出すことが可能
```
    <div class="mb-3">
        <label for="title" class="form-label">タイトル</label>
        <input id="title" type="text" th:field="*{title}" class="form-control"
               th:classappend="${#fields.hasErrors('title')} ? 'is-invalid'">
        <div class="invalid-feedback" th:if="${#fields.hasErrors('title')}" th:errors="*{title}">
            エラーメッセージ
        </div>
    </div>
```

■上部の赤い枠に表示されているエラー（グローバルエラー / すべてのエラー）
- 「どの項目か特定できないエラー」 または 「すべてのエラーのまとめ」 が表示される。（@AssertTrue を使ったメソッドなどがこれに該当）
  - @AssertTrue によるエラーは、Springのデフォルト設定では「グローバルエラー」ではなく、「フィールドエラー」として扱われている
```
<form th:action="@{/view/tasks/{id}/update(id=*{id})}" th:object="${task}" method="post" class="col-md-6">
    <!-- 特定の相関チェックエラーのみ表示 -->
    <div th:if="${#fields.hasErrors('statusDoneValid') or #fields.hasErrors('dateOrderValid')}"
         class="alert alert-danger">
        <ul class="mb-0">
            <li th:if="${#fields.hasErrors('statusDoneValid')}" th:errors="*{statusDoneValid}"></li>
            <li th:if="${#fields.hasErrors('dateOrderValid')}" th:errors="*{dateOrderValid}"></li>
        </ul>
    </div>
```
