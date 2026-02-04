# アノテーション所属・役割まとめ

## 1. Java標準 (JPA / Jakarta EE)
「データ（モノ）」の状態や構造を定義する。
| アノテーション | 役割 |
| :--- | :--- |
| `@Entity` | クラスをDBテーブルとして扱う |
| `@Id` | 主キーを定義する |
| `@Column` | カラム名、長さ、null許容などを設定する |
| `@ManyToOne` | テーブル間のリレーションを定義する |

## 2. Spring Framework / Boot
「機能（動き）」や「部品の管理」を定義する。
| アノテーション | 役割 |
| :--- | :--- |
| `@Component`系 | Springに部品として登録する (`@Service`, `@Repository`等) |
| `@Autowired` | 他の部品を注入する (DI) |
| `@Transactional` | トランザクション管理を任せる |
| `@Value` | 設定ファイル（yml等）から値を取ってくる |

---
### 💡 迷った時の判断基準
**「DBの形」**に関することなら → **Java標準 (JPA)**  
**「プログラムの動かし方」**に関することなら → **Spring**  

- 1. 切り分けの決定打：パッケージ名を見る
コードの先頭にある `import` 文を見れば、一発で正体がわかります。
- `jakarta.persistence.`* (旧 `javax.persistence.`*)
  - **正体**: Javaの標準（JPA）
  - **例**: `@Entity`, `@Id`, `@Column`, `@Enumerated`
  - **役割**:テーブルとJavaのクラスをどう紐付けるか」という**データ構造**の定義。
- `org.springframework.*`
  - **正体**: Spring Framework / Spring Boot
  - **例**: `@Service`, `@RestController`, `@Autowired`, `@Transactional`
  - **役割**: 「どう動くか」「どこに注入するか」といったアプリケーションの挙動の定義。
