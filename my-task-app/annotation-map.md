# アノテーションまとめ

## package com.m_takahisa.taskapp.entity;
| アノテーション | 役割 |
| :--- | :--- |
| `@Entity` | Springに対して「これはエンティティを担当する特別なクラスですよ」と教えるアノテーション |
| `@Table(name = "users")` | PostgreSQLの「users」テーブルに対応 |
| `@Data` | Getter, Setter, toStringなどを自動生成（Lombok） |
| `@Id` | idを明記 |
| `@GeneratedValue(strategy = GenerationType.IDENTITY)` | 自動生成 |
| `@Column(unique = true, nullable = false)` | DB側でもユニークキーの設定、NOT NULL制約を行う。 |
| `@Column(name = "created_at", nullable = false, updatable = false)` | 上記に加え、カラム名も付けられる。 |
| `@Column(columnDefinition = "TEXT", length = 100)` | 上記に加え、カラムの型、長さも付けられる。 |
| `@PrePersist` | メソッドに付けることで、保存前に実装した処理を実施する |
| `@PreUpdate` | メソッドに付けることで、更新前に実装した処理を実施する |
| `@ManyToOne(fetch = FetchType.LAZY)` | Userとの「多対一」のリレーション設定。FetchType.LAZY: タスクを取得する際、必要になるまでユーザー情報を読み込まない（遅延読み込み）設定にすることで、メモリやパフォーマンスの無駄を防ぐ。 |
| `@OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)` | User側からも自分のタスク一覧を参照できる。タスクとユーザーで双方向リレーションの関係。CascadeType.ALL: ユーザーが削除されたときに、その人のタスクも一緒に消える（カスケード削除）設定。 |
| `@JoinColumn(name = "user_id", nullable = false)` | テーブル結合条件 |

## package com.m_takahisa.taskapp.repository;
| アノテーション | 役割 |
| :--- | :--- |
| `@Repository` | Springに対して「これはリポジトリを担当する特別なクラスですよ」と教えるアノテーション |

## package com.m_takahisa.taskapp.service;
| アノテーション | 役割 |
| :--- | :--- |
| `@Service` | Springに対して「これはビジネスロジックを担当する特別なクラスですよ」と教えるアノテーション |
| `@RequiredArgsConstructor` | Lombokの機能。自動でコンストラクタを作成し、Springがリポジトリを自動で注入（DI）してくれる。 |
| `@Transactional` | メソッドに付けることで、データベースの処理を「一塊の作業」として保証。もし途中でエラーが起きても、自動でロールバックしてデータの一貫性を守る。 |

## package com.m_takahisa.taskapp.controller;
| アノテーション | 役割 |
| :--- | :--- |
| `@RestController` | このクラスがWebの窓口（APIの口）であることを示す。戻り値が自動的にJSON形式などでブラウザに返される。 |
| `@RequestMapping("/users")` | コントローラー内のすべてのメソッドのURLの起点を /users に設定。 |
| `@RequiredArgsConstructor` | (省略) |
| `@GetMapping("/test")` | GETメソッド |
