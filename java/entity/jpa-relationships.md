# JPA 関連アノテーション（多重度）比較表

| アノテーション | 意味 | デフォルトFetch | 特徴 | 主な用途・例 |
| :--- | :--- | :--- | :--- | :--- |
| **@ManyToOne** | 多対1 | **EAGER** (即時) | 最もよく使う。 | 投稿(Many) → ユーザー(One) |
| **@OneToMany** | 1対多 | **LAZY** (遅延) | 自分一人に対して、相手が複数。通常、List や Set で保持 | ユーザー(One) → 投稿リスト(Many) |
| **@OneToOne** | 1対1 | **EAGER** (即時) | どちらか一方が相手のID（外部キー）を持つ | ユーザー(One) → プロフィール(One) |
| **@ManyToMany** | 多対多 | **LAZY** (遅延) | 中間に「中間テーブル」が自動で作られる。管理が複雑。 | 学生(Many) → 講義(Many) |

## 実務での重要ポイント
- **@ManyToOne / @OneToOne**: デフォルトが `EAGER` なので、意図しない大量のSQL（N+1問題）を防ぐために、明示的に `fetch = FetchType.LAZY` を設定するのが基本です。
- **@OneToMany**: 通常、`mappedBy` を使用して双方向リレーションを定義します。
- **@ManyToMany**: 管理が複雑になるため、中間テーブル用のEntityを自作して `@OneToMany` と `@ManyToOne` に分解することが推奨されます。

### 実装例
- @OneToOne (1対1)
```java
@OneToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "profile_id")
private UserProfile profile;
```
- @OneToMany (1対多)
```java
@OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
private List<Post> posts;
```
| 引数 | 挙動 | 主な用途 |
| :--- | :--- | :--- |
| **mappedBy** | 双方向の「親」であることを示す | ほぼすべての双方向 `@OneToMany` |
| **cascade = ALL** | 保存も削除も親に任せる | 注文(親)と注文明細(子)のような強い親子関係 |
| **orphanRemoval = true** | リストから消したらDBからも消す | 持ち主が変わることがないデータ（プロフィール画像など） |

**⚠️ 実務のアドバイス**:
強い親子関係（親がいなければ子が存在できない関係）には、この3点セットを全部付けるのが一般的。  
逆に、ユーザーと部署の関係のように「ユーザーが部署から外れても、部署自体は消えてほしくない」場合は、`cascade` や `orphanRemoval` は慎重に設定。  

- @ManyToOne (多対1)
```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "user_id") // DB側の外部キーカラム名
private User user;
```
- @ManyToMany (多対多)
```java
@ManyToMany
@JoinTable(
    name = "student_course",
    joinColumns = @JoinColumn(name = "student_id"),
    inverseJoinColumns = @JoinColumn(name = "course_id")
)
private List<Course> courses;
```
## JPA：単方向リレーション vs 双方向リレーション

| 比較項目 | 単方向 (Unidirectional) | 双方向 (Bidirectional) |
| :--- | :--- | :--- |
| **定義** | 一方のエンティティからのみ相手を参照できる | 両方のエンティティから互いを参照できる |
| **アノテーション** | `@ManyToOne` のみ（子側に記述） | `@ManyToOne` (子) ＋ `@OneToMany` (親) |
| **Javaでの操作** | `post.getUser()` は可能 | `post.getUser()` も `user.getPosts()` も可能 |
| **DB構造** | 子テーブルに外部キー(FK)ができる | **単方向と同じ**（子側にFKができる） |
| **コードの複雑さ** | シンプル（管理が楽） | 複雑（整合性を保つコードが必要） |
| **主なリスク** | 特になし | **循環参照(無限ループ)**、N+1問題 |

### 実務での推奨
- **原則は「単方向」**: 
  「ユーザーから投稿一覧を辿る」必要がないなら、単方向で設計するのが最も安全です。
- **必要に応じて「双方向」**: 
  画面表示の都合上、親から子を一気に取得したい場合のみ双方向に拡張します。  
  ⚠️ 双方向の鉄則 双方向にする場合は、絶対に Lombokの `@Data` を使わない こと。(`StackOverflowError` が発生するため。)  
  
## 「LAZY（遅延読み込み）」vs 「EAGER（即時読み込み）」
| 方式 | 挙動 | メリット | デメリット |
| :--- | :--- | :--- | :--- |
| LAZY | 関連先にアクセスした瞬間にSQLを発行する | メモリ消費が少なく、動作が軽い | 「N+1問題」の原因になる |
| EAGER | 最初のSQLで、関連データも全部まとめて取ってくる | 一度のSQLで全て揃うので確実 | 使わないデータまで取るので、量が多いと激重になる |

## N+1問題
`@ManyToOne` のデフォルト設定 `EAGER（即時）` で、 `Order（注文）` クラスに `User（ユーザー）` が紐付いていた場合...
1. 「注文一覧」を100件取得する。
2. 自動的に、それぞれの注文に紐付く「ユーザー情報」も100回SQLを投げて取ってこようとする。（これが無駄！）
実務では、「注文一覧は見たいけど、ユーザーの詳細は今は不要」というケースが多い  
→ 基本的には 「とりあえず LAZY にしておき、必要なときだけロードする」 のが鉄則
