# daitai-language v1.3 — Formal Specification

**Creator:** Joakim Cöster · joakim@daitai.org  
**Version:** 1.3.1  
**Status:** Normative  
**Date:** 2026-03-30  

---

## 1. Introduction

daitai-language is an algebraic, object-oriented pseudolanguage designed to
specify algorithms clearly and deterministically. It is not executable — it
exists for reasoning, transformation, and transpilation to Java, C++,
TypeScript, Python, Rust, Go, and WebAssembly.

Clarity, determinism, and correctness are strictly more important than expressiveness or convenience.

---

## 2. Authority

This document is normative. If a construct, keyword, or behavior is not
explicitly permitted by this document, it is forbidden.

---

## 3. Design Principles

| # | Principle | Description |
|---|-----------|-------------|
| D1 | Determinism | All expressions are deterministic and referentially transparent |
| D2 | Static typing | Explicit types everywhere, no type inference |
| D3 | Immutability | All data immutable by default |
| D4 | Expression-based | Everything is an expression that returns a value |
| D5 | Language neutrality | No coupling to a specific target language |
| D6 | Purity | All functions and methods are pure |
| D7 | Composition | Composition over inheritance, always |

---

## 4. Lexical Structure

### 4.1 Keywords

```
class    interface   enum        function    method
static   return      if          else        for
in       while       match       case        val
this     import      module      implements  extends
```

### 4.2 Algebraic Keywords (v1.2)

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

### 4.3 Literals

```
42              -- Int
3.14            -- Float
true, false     -- Bool
"hello"         -- String
None            -- Optional<T> (empty)
Some(x)         -- Optional<T> (with value)
/pattern/flags  -- Regex (v1.1)
```

### 4.4 Operators

```
+  -  *  /  %           -- Arithmetic
== != < > <= >=         -- Comparison
&& || !                 -- Logic
|>                      -- Pipeline
.                       -- Member access
```

### 4.5 Comments

```
-- Line comment
{- Block comment -}
```

### 4.6 Indentation

Indentation (whitespace) is significant and defines blocks, similar to Python.

---

## 5. Type System

### 5.1 Base Types

| Type | Description |
|------|-------------|
| `Int` | Integer, arbitrary precision |
| `Float` | Floating point (IEEE 754 double) |
| `Bool` | `true` or `false` |
| `String` | UTF-8 text string |
| `Unit` | Unit value (void equivalent) |

### 5.2 Generic Types

| Type | Description |
|------|-------------|
| `Optional<T>` | `Some(value)` or `None` |
| `Result<T, E>` | `Ok(value)` or `Err(error)` |
| `List<T>` | Immutable ordered sequence |
| `Map<K, V>` | Immutable key-value mapping |
| `Set<T>` | Immutable set |
| `Tuple<A, B, ...>` | Product type with positional access |

### 5.3 Regex (v1.1)

```daitai
val pattern: Regex = /[a-z]+/i
function matches(s: String, r: Regex) -> Bool
function findAll(s: String, r: Regex) -> List<String>
```

### 5.4 Type Variables

Type variables are written in uppercase: `T`, `A`, `B`, `K`, `V`.

```daitai
function map<A, B>(list: List<A>, f: (A) -> B) -> List<B>
```

### 5.5 Value Semantics

- All types are compared structurally
- No reference identity
- No `null` — `Optional<T>` is always used

---

## 6. Declarations

### 6.1 Classes (Product Types)

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

**Rules:**
- All fields are immutable (`val` semantics)
- Methods are pure — `this` is read-only
- Constructors have no logic
- Equality is structural

### 6.2 Interface (Sum Types / Shapes)

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

**Rules:**
- Interfaces have no fields
- Interfaces have no methods with bodies
- No default implementation
- Nominal typing

### 6.3 Enums

```daitai
enum Direction:
    North
    South
    East
    West
```

### 6.4 Algebraic Data Types (v1.1)

```daitai
enum Tree<T>:
    Leaf(value: T)
    Branch(left: Tree<T>, right: Tree<T>)
```

### 6.5 Functions

```daitai
function distance(a: Vec2, b: Vec2) -> Float:
    val dx: Float = b.x - a.x
    val dy: Float = b.y - a.y
    return sqrt(dx * dx + dy * dy)
```

**Rules:**
- Pure — no side effects
- No global variables
- No closures (v1.0) — closures permitted in v1.1 with restrictions

### 6.6 Modules

```daitai
module Geometry:
    class Vec2:
        x: Float
        y: Float
    function dot(a: Vec2, b: Vec2) -> Float:
        return a.x * b.x + a.y * b.y
```

---

## 7. Control Flow

### 7.1 if/else (expression)

```daitai
val result: Int = if x > 0: x else: -x
```

### 7.2 for

```daitai
for item in list:
    process(item)
```

### 7.3 while (restrictive)

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

Match must be exhaustive — all variants are covered.

### 7.5 Forbidden Control Flow

| Construct | Status |
|-----------|--------|
| `break` | ❌ Forbidden |
| `continue` | ❌ Forbidden |
| `goto` | ❌ Forbidden |
| `throw` / `try` / `catch` | ❌ Forbidden |

---

## 8. Error Handling

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

## 9. Algebraic Structures (v1.1)

daitai-language v1.1 has built-in support for algebraic structures from abstract algebra and category theory.

### 9.1 Algebra

| Structure | Keyword | Operations |
|-----------|---------|------------|
| Monoid | `MONOID` | `combine`, `empty` |
| Group | `GROUP` | `combine`, `empty`, `inverse` |
| Ring | `RING` | `add`, `mul`, `zero`, `one`, `negate` |
| Semiring | `SEMIRING` | `add`, `mul`, `zero`, `one` |
| Lattice | `LATTICE` | `join`, `meet`, `top`, `bottom` |
| Galois Connection | `GALOIS` | `lower`, `upper` with adjunction laws |

### 9.2 Category Theory — Fundamentals

| Structure | Keyword | Operations |
|-----------|---------|------------|
| Category | `CATEGORY` | `compose`, `identity` |
| Functor | `FUNCTOR` | `map` (preserves composition/identity) |
| Natural Transform | `NATURALTRANSFORM` | `transform` (naturality condition) |
| Applicative | `APPLICATIVE` | `pure`, `apply` |
| Monad | `MONAD` | `pure`, `flatMap` (+ monad laws) |
| Comonad | `COMONAD` | `extract`, `extend` |
| Free Monad | `FREEMONAD` | `pure`, `liftF`, `foldMap` |

### 9.3 Category Theory — Advanced

| Structure | Keyword | Operations |
|-----------|---------|------------|
| Arrow | `ARROW` | `arr`, `compose`, `first` |
| Arrow Choice | `ARROWCHOICE` | `left`, `right`, `fanin` |
| Arrow Loop | `ARROWLOOP` | `loop` (feedback) |
| Profunctor | `PROFUNCTOR` | `dimap`, `lmap`, `rmap` |
| Bifunctor | `BIFUNCTOR` | `bimap`, `first`, `second` |
| Contravariant | `CONTRAVARIANT` | `contramap` |
| Yoneda | `YONEDA` | `lift`, `lower`, `map` |
| Left Kan Extension | `LEFTKAN` | `extend`, `unit`, `map` |
| Right Kan Extension | `RIGHTKAN` | `extend`, `counit`, `map` |
| Adjunction | `ADJUNCTION` | `unit`, `counit`, `leftAdjunct`, `rightAdjunct` |

### 9.4 Category Theory — Monoidal & Higher

| Structure | Keyword | Operations |
|-----------|---------|------------|
| Monoidal Category | `MONOIDALCATEGORY` | `tensor`, `unit`, `assoc`, `leftUnit`, `rightUnit` |
| String Diagram | `STRINGDIAGRAM` | `compose`, `tensor`, `identity`, `braid` |
| Enriched Category | `ENRICHEDCATEGORY` | `hom`, `compose`, `identity`, `tensor` |
| Traced Monoidal | `TRACEDMONOIDAL` | `trace`, `loop`, `yanking` |
| 2-Category | `TWOCATEGORY` | `vcomp`, `hcomp`, `identity2`, `whiskerL`, `whiskerR` |
| Compact Closed | `COMPACTCLOSED` | `dual`, `eval`, `coeval`, `name`, `coname` |

### 9.5 Other

| Structure | Keyword | Operations |
|-----------|---------|------------|
| Topos | `TOPOS` | `subobject`, `pullback`, `omega`, `classify` |
| Operad | `OPERAD` | `compose`, `identity`, `action` |
| F-Algebra | `FALGEBRA` | `algebra`, `carrier`, `cata` |
| Coalgebra | `COALGEBRA` | `unfold`, `observe` |

### 9.6 Example Syntax

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

## 10. Transpilation Targets

daitai-language transpiles to:

| Target | Format |
|--------|--------|
| Pseudo | Readable pseudocode |
| TypeScript | ES2020+ modules |
| Rust | Structs + traits + impl |
| Go | Structs + receiver methods |
| WASM | WAT (WebAssembly Text Format) |
| Java | Classes + interfaces |
| C++ | Structs + namespaces |
| Python | Dataclasses + type hints |

---

## 11. Forbidden Constructs

| Construct | Status | Alternative |
|-----------|--------|-------------|
| Mutation | ❌ | New copy |
| Behavioral inheritance | ❌ | Composition, traits |
| Virtual dispatch | ❌ | Pattern matching |
| Exceptions | ❌ | `Result<T, E>` |
| Global state | ❌ | Explicit state-passing |
| IO | ❌ | IO boundary / PAL |
| Reflection | ❌ | Static types |
| Macros | ❌ | Functions |
| `null` | ❌ | `Optional<T>` |
| Implicit typing | ❌ | Explicit types |
| `break` / `continue` | ❌ | Recursion / map / filter |

---

## 12. Formal Grammar (EBNF)

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

## 13. Semantic Rules

### 13.1 Scope & Symbol Table
- Each block introduces a new scope
- Shadowing is forbidden
- All names must be declared before use

### 13.2 Type Checking
- All expressions have a static type
- Type variables are unified at instantiation
- No implicit type conversion

### 13.3 Purity Check
- Functions and methods must not mutate state
- No side effects (IO, logging, random)
- `this` is read-only in methods

### 13.4 Control Flow Validation
- All branches in if/else must have the same return type
- Match must be exhaustive
- No unreachable statements

### 13.5 Algebraic Laws
The transpiler SHOULD verify that algebraic declarations satisfy their laws
(associativity, identity, etc.) but this is non-normative in v1.1.

---

## 14. Version History

| Version | Date | Changes |
|---------|------|---------|
| v1.0 | 2026-03-20 | Initial specification |
| v1.1 | 2026-03-28 | Regex literals, ADTs with data, match/pattern matching, 30+ algebraic structures (category theory), closures (restrictive), EBNF grammar, formal semantic rules |
| v1.2 | 2026-03-28 | +DOUBLECATEGORY, +MULTICATEGORY, +MOE, +MULTIMODAL. Total 36 algebraic keywords. GPT-2/3/4/5 transformer specifications as validation. |
| v1.3 | 2026-03-28 | +INFINITYGROUPOID (∞-groupoid for anti-forgetting memory), +SHEAF (local→global coherence), +SPLITCOALGEBRA (neuron-split under stress with Topos Ω-verification). Total 39 algebraic keywords. GPT-7 specification as validation. |

---

## 15. Algebraic Structures — Reference (v1.2)

### 15.1 Fundamental Algebra

| Keyword | Operations | Laws |
|---------|------------|------|
| `MONOID` | `empty`, `combine(a,b)` | Associativity, identity |
| `GROUP` | + `inverse(a)` | + Inverse element |
| `RING` | `add` (group), `mul` (monoid) | Distributivity |
| `SEMIRING` | `add` (monoid), `mul` (monoid) | Distributivity, annihilation |
| `LATTICE` | `meet(a,b)`, `join(a,b)` | Associativity, commutativity, absorption |

### 15.2 Category Theory

| Keyword | Operations | Laws |
|---------|------------|------|
| `CATEGORY` | `identity(a)`, `compose(f,g)` | Associativity, identity |
| `FUNCTOR` | `map(f, fa)` | Preserves identity and composition |
| `NATURALTRANSFORM` | `component(a)` | Naturality condition (commutative diagram) |
| `MONOIDALCATEGORY` | `tensor(f,g)`, `unit`, `associator`, `leftUnitor`, `rightUnitor` | Pentagon, triangle |
| `ENRICHEDCATEGORY` | `hom(a,b)`, `compose`, `identity`, `tensor` | Enriched composition |
| `TRACEDMONOIDAL` | `trace(f)`, `loop(f,init)`, `yanking` | Naturality, yanking, superposition |
| `TWOCATEGORY` | `vCompose`, `hCompose`, `identity2`, `whiskerLeft/Right` | Exchange law |
| `COMPACTCLOSED` | `dual(a)`, `eval`, `coeval`, `name`, `coname` | Duality axioms |
| `DOUBLECATEGORY` | `hCompose`, `vCompose`, `hIdentity`, `vIdentity`, `square` | Exchange law |
| `STRINGDIAGRAM` | `compose`, `tensor`, `identity`, `braid` | Monoidal coherence |

### 15.3 Higher Abstraction

| Keyword | Operations | Laws |
|---------|------------|------|
| `APPLICATIVE` | `pure(a)`, `apply(ff, fa)` | Identity, composition, homomorphism, interchange |
| `MONAD` | `unit(a)`, `flatMap(fa, f)` | Left/right identity, associativity |
| `COMONAD` | `extract(w)`, `extend(f,w)`, `duplicate(w)` | Dual to monad |
| `FREEMONAD` | `pure(a)`, `liftF(fa)`, `foldFree(nat,fm)` | Free construction |
| `ARROW` | `arr(f)`, `first(af)`, `compose(f,g)` | Arrow laws |
| `ARROWCHOICE` | `left(af)`, `right(af)`, `fanin(f,g)` | Choice composition |
| `ARROWLOOP` | `loop(af)` | Fixed-point semantics |
| `PROFUNCTOR` | `dimap(f,g,p)`, `lmap(f,p)`, `rmap(g,p)` | Contravariant/covariant |
| `BIFUNCTOR` | `bimap(f,g,p)`, `first(f,p)`, `second(g,p)` | Bivariant |
| `CONTRAVARIANT` | `contramap(f,p)` | Reversed covariance |

### 15.4 Universal Constructions

| Keyword | Operations | Laws |
|---------|------------|------|
| `YONEDA` | `embed(fa)`, `unembed(nat)` | Yoneda lemma |
| `LEFTKAN` | `extend(fa)`, `counit(ga)` | Universal property |
| `RIGHTKAN` | `lift(fa)`, `unit(ga)` | Universal property |
| `ADJUNCTION` | `unit(a)`, `counit(fa)`, `leftAdjunct`, `rightAdjunct` | Triangle identities |
| `GALOIS` | `alpha(a)`, `gamma(b)`, `floor(b)`, `ceil(a)` | Monotonicity, α ⊣ γ |

### 15.5 Algebraic Data Analysis

| Keyword | Operations | Laws |
|---------|------------|------|
| `COALGEBRA` | `unfold(seed)`, `ana(coalg,seed)`, `observe(state)` | Productive corecursion |
| `FALGEBRA` | `algebra(fa)`, `cata(alg,fix)` | Initiality, universal fold |
| `OPERAD` | `identity(a)`, `compose(f,gs)`, `arity(f)`, `symmetry(f,perm)` | Associativity, Σ_n-equivariance |
| `MULTICATEGORY` | `identity(a)`, `compose(f,gs)`, `arity(f)`, `cut(f,i,g)` | Associativity, identity |
| `TOPOS` | `terminal(a)`, `pullback(f,g)`, `classifier(mono)`, `power(a)` | Subobject classifier, limits |

### 15.6 Domain-Specific (v1.2)

| Keyword | Operations | Laws |
|---------|------------|------|
| `MOE` | `expert(x)`, `gate(x)`, `combine(outputs,weights)`, `route(x)`, `balance(loads)` | Σ_N expert permutation invariance, sparsity, capacity balancing |
| `MULTIMODAL` | `encode(modality,x)`, `fuse(reprs)`, `align(a,b)`, `project(x,target)` + `modalities:` | Coherence (fusion commutes with modality transformations), naturality |

### 15.7 Higher Homotopy Theory & Dynamic Growth (v1.3)

| Keyword | Operations | Laws |
|---------|------------|------|
| `INFINITYGROUPOID` | `cell(level,a,b)`, `compose(p,q)`, `inverse(p)`, `identity(a)`, `coherence(level)` | All morphisms invertible, path-connectivity (∀a,b: ∃p: cell(0,a,b)), higher coherence at every level |
| `SHEAF` | `section(U)`, `restrict(s,V)`, `glue(sections)`, `isCompatible(sections)` | Locality (sections equal on overlap → globally equal), Gluing (compatible sections → global section), Functoriality (restrict∘restrict = restrict) |
| `SPLITCOALGEBRA` | `observe(state)`, `split(neuron,stress)`, `merge(a,b)`, `stress(neuron)`, `verify(pre,post)` | Semantics-preserving (verify(pre,post) ⟹ meaning(pre) ≅ meaning(post)), Stability (post-split stress < threshold), Coalgebraic (observe = S → F(S)) |

**∞-Groupoid** — The foundation of HoTT (Homotopy Type Theory). Types *are* ∞-groupoids. Usage: anti-forgetting memory where knowledge lives in a path-connected space — nothing can become inaccessible.

**Sheaf** — Gluing theory: locally consistent data can always be lifted to globally consistent data. Usage: MoE coherence where each expert has local knowledge that must integrate into a consistent whole. H¹(Sheaf) = 0 ⟹ no conflicts.

**SplitCoalgebra** — Coalgebraic neuron-split: neurons split under stress (rate² > threshold) and are verified via Topos subobject classifier Ω. Usage: dynamic architecture growth without semantic loss.

---

## 16. Future (v1.4, non-normative)

- Higher-Kinded Types
- Effect System (algebraic effects)
- Dependent Types (limited)
- Linear Types
- Formal verification of algebraic laws
- ∞-Category (full ∞-category theory)
- Higher Operad (∞-operad)
- Spectral Sequences (homological algebra)
- Persistent Homology (topological data analysis)
