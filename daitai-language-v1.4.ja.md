# daitai-language v1.4 — 正式仕様書

**Creator:** Joakim Cöster · joakim@daitai.org  
**バージョン:** 1.3.1  
**ステータス:** 規範的  
**日付:** 2026-03-30  

---

## 1. はじめに

daitai-languageは、アルゴリズムを明確かつ決定論的に仕様記述するために設計された代数的オブジェクト指向疑似言語です。実行可能ではなく、推論・変換・Java、C++、TypeScript、Python、Rust、Go、WebAssemblyへのトランスパイルのために存在します。

明確性、決定論、正確性は、表現力や利便性よりも厳密に重要です。

---

## 2. 権威

本文書は規範的です。本文書で明示的に許可されていない構文、キーワード、動作は禁止されています。

---

## 3. 設計原則

| # | 原則 | 説明 |
|---|------|------|
| D1 | 決定論 | すべての式は決定論的かつ参照透過 |
| D2 | 静的型付け | すべての箇所で明示的な型、型推論なし |
| D3 | 不変性 | すべてのデータはデフォルトで不変 |
| D4 | 式ベース | すべてが値を返す式 |
| D5 | 言語中立性 | 特定のターゲット言語への結合なし |
| D6 | 純粋性 | すべての関数とメソッドはpure |
| D7 | 合成 | 常に継承より合成 |

---

## 4. 字句構造

### 4.1 キーワード

```
class    interface   enum        function    method
static   return      if          else        for
in       while       match       case        val
this     import      module      implements  extends
```

### 4.2 代数キーワード (v1.2)

```
MONOID        GROUP         RING          SEMIRING
LATTICE       CATEGORY      FUNCTOR       APPLICATIVE
MONAD         COMONAD       FREEMONAD     ARROW
ARROWCHOICE   ARROWLOOP     PROFUNCTOR    BIFUNCTOR
CONTRAVARIANT NATURALTRANSFORM  COALGEBRA
YONEDA        LEFTKAN       RIGHTKAN      ADJUNCTION
GALOIS        TOPOS         OPERAD        FALGEBRA
MONOIDALCATEGORY  STRINGDIAGRAM
ENRICHEDCATEGORY  TRACEDMONOIDAL
TWOCATEGORY       COMPACTCLOSED
DOUBLECATEGORY    MULTICATEGORY
MOE               MULTIMODAL
INFINITYGROUPOID  SHEAF
SPLITCOALGEBRA
```

### 4.3 リテラル

```
42              -- Int
3.14            -- Float
true, false     -- Bool
"hello"         -- String
None            -- Optional<T> (空)
Some(x)         -- Optional<T> (値あり)
/pattern/flags  -- Regex (v1.1)
```

### 4.4 演算子

```
+  -  *  /  %           -- 算術
== != < > <= >=         -- 比較
&& || !                 -- 論理
|>                      -- パイプライン
.                       -- メンバーアクセス
```

### 4.5 コメント

```
-- 行コメント
{- ブロックコメント -}
```

### 4.6 インデント

インデント（空白）は有意であり、Pythonと同様にブロックを定義します。

---

## 5. 型システム

### 5.1 基本型

| 型 | 説明 |
|----|------|
| `Int` | 整数、任意精度 |
| `Float` | 浮動小数点数 (IEEE 754 double) |
| `Bool` | `true` または `false` |
| `String` | UTF-8テキスト文字列 |
| `Unit` | 単位値 (voidに相当) |

### 5.2 ジェネリック型

| 型 | 説明 |
|----|------|
| `Optional<T>` | `Some(value)` または `None` |
| `Result<T, E>` | `Ok(value)` または `Err(error)` |
| `List<T>` | 不変順序付きシーケンス |
| `Map<K, V>` | 不変キー・バリューマッピング |
| `Set<T>` | 不変集合 |
| `Tuple<A, B, ...>` | 位置アクセスの積型 |

### 5.3 Regex (v1.1)

```daitai
val pattern: Regex = /[a-z]+/i
function matches(s: String, r: Regex) -> Bool
function findAll(s: String, r: Regex) -> List<String>
```

### 5.4 型変数

型変数は大文字で記述: `T`, `A`, `B`, `K`, `V`。

```daitai
function map<A, B>(list: List<A>, f: (A) -> B) -> List<B>
```

### 5.5 値セマンティクス

- すべての型は構造的に比較される
- 参照同一性なし
- `null`なし — 常に`Optional<T>`を使用

---

## 6. 宣言

### 6.1 クラス（積型）

```daitai
class Vec3:
    x: Float
    y: Float
    z: Float

    method add(other: Vec3) -> Vec3:
        return Vec3(x = this.x + other.x, y = this.y + other.y, z = this.z + other.z)

    static method zero() -> Vec3:
        return Vec3(x = 0.0, y = 0.0, z = 0.0)
```

**規則:**
- すべてのフィールドは不変（`val`セマンティクス）
- メソッドはpure — `this`は読み取り専用
- コンストラクタにロジックなし
- 等価性は構造的

### 6.2 Interface（和型 / シェイプ）

```daitai
interface Shape

class Circle implements Shape:
    center: Vec2
    radius: Float

class Rectangle implements Shape:
    topLeft: Vec2
    width: Float
    height: Float
```

**規則:**
- インターフェースにフィールドなし
- インターフェースに本体を持つメソッドなし
- デフォルト実装なし
- 名前的型付け

### 6.3 Enum

```daitai
enum Direction:
    North
    South
    East
    West
```

### 6.4 代数的データ型 (v1.1)

```daitai
enum Tree<T>:
    Leaf(value: T)
    Branch(left: Tree<T>, right: Tree<T>)
```

### 6.5 関数

```daitai
function distance(a: Vec2, b: Vec2) -> Float:
    val dx: Float = b.x - a.x
    val dy: Float = b.y - a.y
    return sqrt(dx * dx + dy * dy)
```

**規則:**
- Pure — 副作用なし
- グローバル変数なし
- クロージャなし (v1.0) — v1.1で制限付きクロージャ許可

### 6.6 モジュール

```daitai
module Geometry:
    class Vec2:
        x: Float
        y: Float
    function dot(a: Vec2, b: Vec2) -> Float:
        return a.x * b.x + a.y * b.y
```

---

## 7. 制御フロー

### 7.1 if/else（式）

```daitai
val result: Int = if x > 0: x else: -x
```

### 7.2 for

```daitai
for item in list:
    process(item)
```

### 7.3 while（制限的）

```daitai
while condition:
    body
```

### 7.4 match (v1.1)

```daitai
match shape:
    case Circle(c, r):
        return pi * r * r
    case Rectangle(tl, w, h):
        return w * h
```

matchは網羅的でなければなりません — すべてのバリアントをカバーします。

### 7.5 禁止された制御フロー

| 構文 | ステータス |
|------|------------|
| `break` | ❌ 禁止 |
| `continue` | ❌ 禁止 |
| `goto` | ❌ 禁止 |
| `throw` / `try` / `catch` | ❌ 禁止 |

---

## 8. エラーハンドリング

```daitai
enum ParseError:
    InvalidFormat
    Overflow

function parseInt(s: String) -> Result<Int, ParseError>:
    if isValid(s):
        return Ok(toInt(s))
    else:
        return Err(ParseError.InvalidFormat)
```

---

## 9. 代数構造 (v1.1)

daitai-language v1.1は、抽象代数と圏論の代数構造を組み込みでサポートしています。

### 9.1 代数

| 構造 | キーワード | 操作 |
|------|------------|------|
| Monoid | `MONOID` | `combine`, `empty` |
| Group | `GROUP` | `combine`, `empty`, `inverse` |
| Ring | `RING` | `add`, `mul`, `zero`, `one`, `negate` |
| Semiring | `SEMIRING` | `add`, `mul`, `zero`, `one` |
| Lattice | `LATTICE` | `join`, `meet`, `top`, `bottom` |
| Galois Connection | `GALOIS` | `lower`, `upper` 随伴律 |

### 9.2 圏論 — 基礎

| 構造 | キーワード | 操作 |
|------|------------|------|
| Category | `CATEGORY` | `compose`, `identity` |
| Functor | `FUNCTOR` | `map`（合成と恒等を保存） |
| Natural Transform | `NATURALTRANSFORM` | `transform`（自然性条件） |
| Applicative | `APPLICATIVE` | `pure`, `apply` |
| Monad | `MONAD` | `pure`, `flatMap`（+ モナド則） |
| Comonad | `COMONAD` | `extract`, `extend` |
| Free Monad | `FREEMONAD` | `pure`, `liftF`, `foldMap` |

### 9.3 圏論 — 上級

| 構造 | キーワード | 操作 |
|------|------------|------|
| Arrow | `ARROW` | `arr`, `compose`, `first` |
| Arrow Choice | `ARROWCHOICE` | `left`, `right`, `fanin` |
| Arrow Loop | `ARROWLOOP` | `loop`（フィードバック） |
| Profunctor | `PROFUNCTOR` | `dimap`, `lmap`, `rmap` |
| Bifunctor | `BIFUNCTOR` | `bimap`, `first`, `second` |
| Contravariant | `CONTRAVARIANT` | `contramap` |
| Yoneda | `YONEDA` | `lift`, `lower`, `map` |
| Left Kan Extension | `LEFTKAN` | `extend`, `unit`, `map` |
| Right Kan Extension | `RIGHTKAN` | `extend`, `counit`, `map` |
| Adjunction | `ADJUNCTION` | `unit`, `counit`, `leftAdjunct`, `rightAdjunct` |

### 9.4 圏論 — モノイダル圏 & 高次

| 構造 | キーワード | 操作 |
|------|------------|------|
| Monoidal Category | `MONOIDALCATEGORY` | `tensor`, `unit`, `assoc`, `leftUnit`, `rightUnit` |
| String Diagram | `STRINGDIAGRAM` | `compose`, `tensor`, `identity`, `braid` |
| Enriched Category | `ENRICHEDCATEGORY` | `hom`, `compose`, `identity`, `tensor` |
| Traced Monoidal | `TRACEDMONOIDAL` | `trace`, `loop`, `yanking` |
| 2-Category | `TWOCATEGORY` | `vcomp`, `hcomp`, `identity2`, `whiskerL`, `whiskerR` |
| Compact Closed | `COMPACTCLOSED` | `dual`, `eval`, `coeval`, `name`, `coname` |

### 9.5 その他

| 構造 | キーワード | 操作 |
|------|------------|------|
| Topos | `TOPOS` | `subobject`, `pullback`, `omega`, `classify` |
| Operad | `OPERAD` | `compose`, `identity`, `action` |
| F-Algebra | `FALGEBRA` | `algebra`, `carrier`, `cata` |
| Coalgebra | `COALGEBRA` | `unfold`, `observe` |

### 9.6 構文例

```daitai
MONOID StringConcat over String:
    combine(a, b) = a + b
    empty = ""

FUNCTOR ListF<A, B>:
    map(f: (A) -> B, fa: List<A>) -> List<B>:
        return fa.map(f)

MONAD MaybeM<A>:
    pure(a: A) -> Optional<A>:
        return Some(a)
    flatMap(ma: Optional<A>, f: (A) -> Optional<B>) -> Optional<B>:
        match ma:
            case Some(a): return f(a)
            case None: return None

TWOCATEGORY Cat2:
    vcomp(alpha, beta) = verticalCompose(alpha, beta)
    hcomp(alpha, beta) = horizontalCompose(alpha, beta)
    identity2(f) = identityTwoCell(f)
    whiskerL(f, alpha) = leftWhisker(f, alpha)
    whiskerR(alpha, f) = rightWhisker(alpha, f)
```

---

## 10. トランスパイルターゲット

daitai-languageは以下にトランスパイルします：

| ターゲット | 形式 |
|------------|------|
| Pseudo | 可読擬似コード |
| TypeScript | ES2020+ モジュール |
| Rust | Structs + traits + impl |
| Go | Structs + receiver methods |
| WASM | WAT (WebAssembly Text Format) |
| Java | Classes + interfaces |
| C++ | Structs + namespaces |
| Python | Dataclasses + type hints |

---

## 11. 禁止された構文

| 構文 | ステータス | 代替 |
|------|------------|------|
| ミューテーション | ❌ | 新しいコピー |
| 動作継承 | ❌ | 合成、traits |
| 仮想ディスパッチ | ❌ | パターンマッチング |
| 例外 | ❌ | `Result<T, E>` |
| グローバル状態 | ❌ | 明示的な状態渡し |
| IO | ❌ | IO境界 / PAL |
| リフレクション | ❌ | 静的型 |
| マクロ | ❌ | 関数 |
| `null` | ❌ | `Optional<T>` |
| 暗黙の型付け | ❌ | 明示的な型 |
| `break` / `continue` | ❌ | 再帰 / map / filter |

---

## 12. 正式文法 (EBNF)

```ebnf
program        = { declaration } ;

declaration    = classDecl
               | interfaceDecl
               | enumDecl
               | functionDecl
               | moduleDecl
               | algebraDecl ;

classDecl      = "class" IDENT [ typeParams ] [ "implements" IDENT ] ":"
                   INDENT { fieldDecl | methodDecl | staticMethod } DEDENT ;

interfaceDecl  = "interface" IDENT [ typeParams ] ;

enumDecl       = "enum" IDENT [ typeParams ] ":"
                   INDENT { enumVariant } DEDENT ;

enumVariant    = IDENT [ "(" fieldList ")" ] ;

functionDecl   = "function" IDENT [ typeParams ] "(" paramList ")" "->" typeRef ":"
                   INDENT block DEDENT ;

moduleDecl     = "module" IDENT ":"
                   INDENT { declaration } DEDENT ;

algebraDecl    = algebraKW IDENT [ algebraOver ] [ typeParams ] ":"
                   INDENT { operationDecl } DEDENT ;

algebraKW      = "MONOID" | "GROUP" | "RING" | "SEMIRING" | "LATTICE"
               | "CATEGORY" | "FUNCTOR" | "APPLICATIVE" | "MONAD"
               | "COMONAD" | "FREEMONAD" | "ARROW" | "ARROWCHOICE"
               | "ARROWLOOP" | "PROFUNCTOR" | "BIFUNCTOR" | "CONTRAVARIANT"
               | "NATURALTRANSFORM" | "COALGEBRA" | "YONEDA"
               | "LEFTKAN" | "RIGHTKAN" | "ADJUNCTION" | "GALOIS"
               | "TOPOS" | "OPERAD" | "FALGEBRA"
               | "MONOIDALCATEGORY" | "STRINGDIAGRAM"
               | "ENRICHEDCATEGORY" | "TRACEDMONOIDAL"
               | "TWOCATEGORY" | "COMPACTCLOSED"
               | "DOUBLECATEGORY" | "MULTICATEGORY"
               | "MOE" | "MULTIMODAL"
               | "INFINITYGROUPOID" | "SHEAF" | "SPLITCOALGEBRA" ;

algebraOver    = "over" typeRef ;

fieldDecl      = IDENT ":" typeRef ;

methodDecl     = "method" IDENT "(" paramList ")" "->" typeRef ":"
                   INDENT block DEDENT ;

staticMethod   = "static" methodDecl ;

operationDecl  = IDENT "(" paramList ")" [ "->" typeRef ] "=" expr ;

typeRef        = IDENT [ "<" typeRef { "," typeRef } ">" ]
               | "(" typeRef { "," typeRef } ")" "->" typeRef ;

typeParams     = "<" IDENT { "," IDENT } ">" ;

paramList      = [ param { "," param } ] ;
param          = IDENT ":" typeRef ;

block          = { statement } ;

statement      = valDecl
               | returnStmt
               | ifExpr
               | forLoop
               | whileLoop
               | matchExpr
               | expr ;

valDecl        = "val" IDENT ":" typeRef "=" expr ;
returnStmt     = "return" expr ;

ifExpr         = "if" expr ":" INDENT block DEDENT
                 [ "else" ":" INDENT block DEDENT ] ;

forLoop        = "for" IDENT "in" expr ":" INDENT block DEDENT ;
whileLoop      = "while" expr ":" INDENT block DEDENT ;

matchExpr      = "match" expr ":"
                   INDENT { matchCase } DEDENT ;
matchCase      = "case" pattern ":" INDENT block DEDENT ;

pattern        = IDENT [ "(" pattern { "," pattern } ")" ]
               | literal ;

expr           = literal
               | IDENT
               | expr "." IDENT
               | expr "(" argList ")"
               | expr binOp expr
               | unaryOp expr
               | expr "|>" expr
               | "(" expr ")"
               | ifExpr
               | matchExpr ;

literal        = INT | FLOAT | STRING | "true" | "false" | "None" | regexLit ;
regexLit       = "/" regexBody "/" { regexFlag } ;

binOp          = "+" | "-" | "*" | "/" | "%" | "==" | "!=" | "<" | ">"
               | "<=" | ">=" | "&&" | "||" ;
unaryOp        = "-" | "!" ;
```

---

## 13. 意味規則

### 13.1 スコープとシンボルテーブル
- 各ブロックが新しいスコープを導入
- シャドウイングは禁止
- すべての名前は使用前に宣言が必要

### 13.2 型検査
- すべての式は静的型を持つ
- 型変数はインスタンス化時に統一される
- 暗黙の型変換なし

### 13.3 純粋性チェック
- 関数とメソッドは状態をミューテートしてはならない
- 副作用なし（IO、ロギング、乱数）
- メソッド内で`this`は読み取り専用

### 13.4 制御フロー検証
- if/elseのすべての分岐は同じ戻り値型でなければならない
- matchは網羅的でなければならない
- 到達不能な文は禁止

### 13.5 代数法則
トランスパイラは代数宣言がその法則（結合律、恒等律等）を満たすことを検証すべき(SHOULD)ですが、これはv1.1では非規範的です。

---

## 14. バージョン履歴

| バージョン | 日付 | 変更 |
|------------|------|------|
| v1.0 | 2025-12-24 | 初版仕様 |
| v1.1 | 2026-03-28 | Regexリテラル、データ付きADT、match/パターンマッチング、30以上の代数構造（圏論）、クロージャ（制限付き）、EBNF文法、正式意味規則 |
| v1.2 | 2026-03-28 | +DOUBLECATEGORY, +MULTICATEGORY, +MOE, +MULTIMODAL。合計36代数キーワード。GPT-2/3/4/5 transformer仕様による検証。 |
| v1.3 | 2026-03-28 | +INFINITYGROUPOID (忘却防止メモリ用∞-groupoid), +SHEAF (局所→大域コヒーレンス), +SPLITCOALGEBRA (Topos Ω検証によるストレス下ニューロン分裂)。合計39代数キーワード。GPT-7仕様による検証。 |
| v1.4 | 2026-03-31 | +KET, +BRA, +OPERATOR, +COMPLEX, +HILBERTSPACE, +OBSERVABLE, +MEASUREMENT, +UNITARYGROUP, +DENSITYMATRIX, +QUANTUMCHANNEL。ネイティブDiracブラケット記法。量子ゲート。量子+トポス結合。合計49代数キーワード。 |

---

## 15. 代数構造 — リファレンス (v1.2)

### 15.1 基礎代数

| キーワード | 操作 | 法則 |
|------------|------|------|
| `MONOID` | `empty`, `combine(a,b)` | 結合律、恒等律 |
| `GROUP` | + `inverse(a)` | + 逆元 |
| `RING` | `add` (群), `mul` (モノイド) | 分配律 |
| `SEMIRING` | `add` (モノイド), `mul` (モノイド) | 分配律、零化 |
| `LATTICE` | `meet(a,b)`, `join(a,b)` | 結合律、交換律、吸収律 |

### 15.2 圏論

| キーワード | 操作 | 法則 |
|------------|------|------|
| `CATEGORY` | `identity(a)`, `compose(f,g)` | 結合律、恒等律 |
| `FUNCTOR` | `map(f, fa)` | 恒等と合成を保存 |
| `NATURALTRANSFORM` | `component(a)` | 自然性条件（可換図式） |
| `MONOIDALCATEGORY` | `tensor(f,g)`, `unit`, `associator`, `leftUnitor`, `rightUnitor` | 五角形、三角形 |
| `ENRICHEDCATEGORY` | `hom(a,b)`, `compose`, `identity`, `tensor` | 豊饒圏合成 |
| `TRACEDMONOIDAL` | `trace(f)`, `loop(f,init)`, `yanking` | 自然性、yanking、重ね合わせ |
| `TWOCATEGORY` | `vCompose`, `hCompose`, `identity2`, `whiskerLeft/Right` | 交換法則 |
| `COMPACTCLOSED` | `dual(a)`, `eval`, `coeval`, `name`, `coname` | 双対性公理 |
| `DOUBLECATEGORY` | `hCompose`, `vCompose`, `hIdentity`, `vIdentity`, `square` | 交換法則 |
| `STRINGDIAGRAM` | `compose`, `tensor`, `identity`, `braid` | モノイダルコヒーレンス |

### 15.3 高次抽象

| キーワード | 操作 | 法則 |
|------------|------|------|
| `APPLICATIVE` | `pure(a)`, `apply(ff, fa)` | 恒等律、合成律、準同型、交換律 |
| `MONAD` | `unit(a)`, `flatMap(fa, f)` | 左右恒等律、結合律 |
| `COMONAD` | `extract(w)`, `extend(f,w)`, `duplicate(w)` | モナドの双対 |
| `FREEMONAD` | `pure(a)`, `liftF(fa)`, `foldFree(nat,fm)` | 自由構成 |
| `ARROW` | `arr(f)`, `first(af)`, `compose(f,g)` | Arrow則 |
| `ARROWCHOICE` | `left(af)`, `right(af)`, `fanin(f,g)` | 選択合成 |
| `ARROWLOOP` | `loop(af)` | 不動点意味論 |
| `PROFUNCTOR` | `dimap(f,g,p)`, `lmap(f,p)`, `rmap(g,p)` | 反変/共変 |
| `BIFUNCTOR` | `bimap(f,g,p)`, `first(f,p)`, `second(g,p)` | 双変 |
| `CONTRAVARIANT` | `contramap(f,p)` | 逆共変性 |

### 15.4 普遍的構成

| キーワード | 操作 | 法則 |
|------------|------|------|
| `YONEDA` | `embed(fa)`, `unembed(nat)` | 米田の補題 |
| `LEFTKAN` | `extend(fa)`, `counit(ga)` | 普遍性 |
| `RIGHTKAN` | `lift(fa)`, `unit(ga)` | 普遍性 |
| `ADJUNCTION` | `unit(a)`, `counit(fa)`, `leftAdjunct`, `rightAdjunct` | 三角恒等式 |
| `GALOIS` | `alpha(a)`, `gamma(b)`, `floor(b)`, `ceil(a)` | 単調性、α ⊣ γ |

### 15.5 代数的データ分析

| キーワード | 操作 | 法則 |
|------------|------|------|
| `COALGEBRA` | `unfold(seed)`, `ana(coalg,seed)`, `observe(state)` | 生産的余再帰 |
| `FALGEBRA` | `algebra(fa)`, `cata(alg,fix)` | 始代数性、普遍fold |
| `OPERAD` | `identity(a)`, `compose(f,gs)`, `arity(f)`, `symmetry(f,perm)` | 結合律、Σ_n-同変性 |
| `MULTICATEGORY` | `identity(a)`, `compose(f,gs)`, `arity(f)`, `cut(f,i,g)` | 結合律、恒等律 |
| `TOPOS` | `terminal(a)`, `pullback(f,g)`, `classifier(mono)`, `power(a)` | 部分対象分類子、極限 |

### 15.6 ドメイン固有 (v1.2)

| キーワード | 操作 | 法則 |
|------------|------|------|
| `MOE` | `expert(x)`, `gate(x)`, `combine(outputs,weights)`, `route(x)`, `balance(loads)` | Σ_Nエキスパート置換不変性、スパース性、容量バランシング |
| `MULTIMODAL` | `encode(modality,x)`, `fuse(reprs)`, `align(a,b)`, `project(x,target)` + `modalities:` | コヒーレンス（融合がモダリティ変換と可換）、自然性 |

### 15.7 高次ホモトピー理論 & 動的成長 (v1.3)

| キーワード | 操作 | 法則 |
|------------|------|------|
| `INFINITYGROUPOID` | `cell(level,a,b)`, `compose(p,q)`, `inverse(p)`, `identity(a)`, `coherence(level)` | すべての射が可逆、path-connectivity (∀a,b: ∃p: cell(0,a,b))、各レベルでの高次コヒーレンス |
| `SHEAF` | `section(U)`, `restrict(s,V)`, `glue(sections)`, `isCompatible(sections)` | 局所性（重なりで等しいセクション → 大域的に等しい）、接合（互換セクション → 大域セクション）、関手性（restrict∘restrict = restrict） |
| `SPLITCOALGEBRA` | `observe(state)`, `split(neuron,stress)`, `merge(a,b)`, `stress(neuron)`, `verify(pre,post)` | 意味保存 (verify(pre,post) ⟹ meaning(pre) ≅ meaning(post))、安定性 (分裂後ストレス < 閾値)、余代数的 (observe = S → F(S)) |

**∞-Groupoid** — HoTT（ホモトピー型理論）の基礎。型は∞-groupoidです。用途：知識がpath-connected空間に存在し、何もアクセス不能にならない忘却防止メモリ。

**Sheaf** — 接合理論：局所的に整合的なデータは常に大域的に整合的なデータに持ち上げ可能。用途：各エキスパートが局所知識を持ち、整合的な全体に統合されるMoEコヒーレンス。H¹(Sheaf) = 0 ⟹ 矛盾なし。

**SplitCoalgebra** — 余代数的ニューロン分裂：ニューロンがストレス下で分裂し (rate² > 閾値)、Topos部分対象分類子Ωで検証。用途：意味損失なしの動的アーキテクチャ成長。

---

## 16. 量子力学的代数 (v1.4)

### 16.1 ヒルベルト空間ソート

v1.4はディラック記法リテラルと量子計算のための代数的ソートを導入します：

```
Ket        -- |ψ⟩  (ヒルベルト空間の列ベクトル)
Bra        -- ⟨φ|  (行ベクトル、Ketの随伴)
Operator   -- 線形演算子 A: H → H
Complex    -- 複素数 a + bi
```

### 16.2 量子リテラル

```
|0⟩, |1⟩, |+⟩, |−⟩       -- 標準量子ビット
|ψ⟩ = α|0⟩ + β|1⟩        -- 重ね合わせ
⟨φ| = ⟨0|γ* + ⟨1|δ*      -- ブラ (共役転置)
⟨φ|ψ⟩                     -- 内積 (スカラー ∈ Complex)
|ψ⟩⟨φ|                    -- 外積 (Operator)
```

### 16.3 キーワード

```
KET           BRA           OPERATOR      COMPLEX
HILBERTSPACE  OBSERVABLE    MEASUREMENT
UNITARYGROUP  DENSITYMATRIX QUANTUMCHANNEL
```

### 16.4 代数的構造

| キーワード | 操作 | 法則 |
|-----------|------|------|
| `KET` | `add(a,b)`, `scale(α,v)`, `norm(v)`, `inner(u,v)`, `tensor(u,v)` | 線形性、⟨ψ\|ψ⟩ ≥ 0、‖ψ‖ = 1 (正規化) |
| `BRA` | `adjoint(ket)`, `inner(bra,ket)`, `scale(α,b)` | ⟨φ\| = (\|φ⟩)†、第一引数の反線形性 |
| `OPERATOR` | `apply(A,ψ)`, `compose(A,B)`, `adjoint(A)`, `commutator(A,B)`, `tensor(A,B)` | 線形性、(AB)† = B†A†、[A,B] = AB - BA |
| `HILBERTSPACE` | `dim(H)`, `basis(H)`, `project(ψ,subspace)`, `directSum(H1,H2)`, `tensorProduct(H1,H2)` | 完備性、分離可能性、内積の正値性 |
| `OBSERVABLE` | `eigenvalues(O)`, `eigenstates(O)`, `expectation(O,ψ)`, `uncertainty(O,ψ)` | エルミート性 (O = O†)、スペクトル定理 |
| `MEASUREMENT` | `measure(ψ,basis)`, `collapse(ψ,outcome)`, `probability(ψ,outcome)` | ボルンの規則 (P = \|⟨outcome\|ψ⟩\|²)、Σ P_i = 1 |
| `UNITARYGROUP` | `identity(n)`, `compose(U,V)`, `inverse(U)`, `det(U)` | UU† = I、\|det(U)\| = 1 |
| `DENSITYMATRIX` | `pure(ψ)`, `mixed(states,probs)`, `trace(ρ)`, `purity(ρ)`, `entropy(ρ)` | Tr(ρ) = 1、ρ ≥ 0、エルミート |
| `QUANTUMCHANNEL` | `apply(E,ρ)`, `kraus(operators)`, `isCP(E)`, `isTP(E)` | 完全正値性、トレース保存 (Σ E_k†E_k = I) |

### 16.5 ディラック代数

以下の演算子恒等式はコンパイル時に検証されます：

```
⟨φ|ψ⟩ = (⟨ψ|φ⟩)*                    -- 共役対称性
⟨φ|(α|ψ₁⟩ + β|ψ₂⟩) = α⟨φ|ψ₁⟩ + β⟨φ|ψ₂⟩  -- 線形性
(|ψ⟩⟨φ|)† = |φ⟩⟨ψ|                   -- 外積の随伴
Σ_i |i⟩⟨i| = I                       -- 完全性関係
```

### 16.6 量子ゲート (事前定義演算子)

```
PAULIX    = |0⟩⟨1| + |1⟩⟨0|           -- パウリX (NOT)
PAULIY    = -i|0⟩⟨1| + i|1⟩⟨0|        -- パウリY
PAULIZ    = |0⟩⟨0| - |1⟩⟨1|           -- パウリZ
HADAMARD  = (|0⟩⟨0| + |0⟩⟨1| + |1⟩⟨0| - |1⟩⟨1|) / √2
CNOT      = |00⟩⟨00| + |01⟩⟨01| + |10⟩⟨11| + |11⟩⟨10|
```

**量子+トポス結合:** MEASUREMENTはトポスの部分対象分類子Ωを使用して、測定結果を直観主義論理の真理値として形式的に処理します。ボルンの規則は、DENSITYMATRIXから確率分布のSHEAFへの自然変換として表現されます。

---

## 17. 将来 (v1.5、非規範的)

- 高カインド型
- エフェクトシステム (代数的エフェクト)
- 依存型 (制限付き)
- 線形型 (量子ノークローニング定理)
- 代数法則の形式検証
- ∞-圏 (完全な∞-圏論)
- 高次オペラド (∞-オペラド)
- スペクトル系列 (ホモロジー代数)
- 永続的ホモロジー (位相的データ解析)
- 量子誤り訂正 (安定化符号)
- 位相的量子計算 (エニオン、組紐群)
