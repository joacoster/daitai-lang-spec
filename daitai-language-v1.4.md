# daitai-language v1.4 — Formell Specifikation

**Creator:** Joakim Cöster · joakim@daitai.org  
**Version:** 1.3.1  
**Status:** Normativ  
**Datum:** 2026-03-30  

---

## 1. Introduktion

daitai-language är ett algebraiskt, objektorienterat pseudospråk designat för att
specificera algoritmer klart och deterministiskt. Det är inte exekverbart — det
existerar för resonemang, transformation och transpilering till Java, C++,
TypeScript, Python, Rust, Go och WebAssembly.

Klarhet, determinism och korrekthet är strikt viktigare än expressivitet eller bekvämlighet.

---

## 2. Auktoritet

Detta dokument är normativt. Om en konstruktion, nyckelord eller beteende inte
uttryckligen tillåts av detta dokument, är det förbjudet.

---

## 3. Designprinciper

| # | Princip | Beskrivning |
|---|---------|-------------|
| D1 | Determinism | Alla uttryck är deterministiska och referentiellt transparenta |
| D2 | Statisk typning | Explicita typer överallt, ingen typinferens |
| D3 | Immutabilitet | All data immutable som default |
| D4 | Uttrycksbaserat | Allt är uttryck som returnerar värden |
| D5 | Språkneutralitet | Ingen koppling till specifikt målspråk |
| D6 | Renhet | Alla funktioner och metoder är pure |
| D7 | Komposition | Komposition över arv, alltid |

---

## 4. Lexikal Struktur

### 4.1 Nyckelord

```
class    interface   enum        function    method
static   return      if          else        for
in       while       match       case        val
this     import      module      implements  extends
```

### 4.2 Algebraiska nyckelord (v1.2)

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

### 4.3 Literaler

```
42              -- Int
3.14            -- Float
true, false     -- Bool
"hello"         -- String
None            -- Optional<T> (tom)
Some(x)         -- Optional<T> (med värde)
/pattern/flags  -- Regex (v1.1)
```

### 4.4 Operatorer

```
+  -  *  /  %           -- Aritmetik
== != < > <= >=         -- Jämförelse
&& || !                 -- Logik
|>                      -- Pipeline
.                       -- Medlemsåtkomst
```

### 4.5 Kommentarer

```
-- Radkommentar
{- Blockkommentar -}
```

### 4.6 Indragning

Indragning (whitespace) är signifikant och definierar block, liknande Python.

---

## 5. Typsystem

### 5.1 Grundtyper

| Typ | Beskrivning |
|-----|-------------|
| `Int` | Heltal, godtycklig precision |
| `Float` | Flyttal (IEEE 754 double) |
| `Bool` | `true` eller `false` |
| `String` | UTF-8 textsträng |
| `Unit` | Enhetsvärde (void-ekvivalent) |

### 5.2 Generiska Typer

| Typ | Beskrivning |
|-----|-------------|
| `Optional<T>` | `Some(value)` eller `None` |
| `Result<T, E>` | `Ok(value)` eller `Err(error)` |
| `List<T>` | Immutabel ordnad sekvens |
| `Map<K, V>` | Immutabel nyckel-värde-mappning |
| `Set<T>` | Immutabel mängd |
| `Tuple<A, B, ...>` | Produkttyp med positionell åtkomst |

### 5.3 Regex (v1.1)

```daitai
val pattern: Regex = /[a-z]+/i
function matches(s: String, r: Regex) -> Bool
function findAll(s: String, r: Regex) -> List<String>
```

### 5.4 Typvariabler

Typvariabler skrivs med stora bokstäver: `T`, `A`, `B`, `K`, `V`.

```daitai
function map<A, B>(list: List<A>, f: (A) -> B) -> List<B>
```

### 5.5 Value Semantics

- Alla typer jämförs strukturellt
- Ingen referensidentitet
- Ingen `null` — `Optional<T>` används alltid

---

## 6. Deklarationer

### 6.1 Klasser (Produkttyper)

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

**Regler:**
- Alla fält är immutabla (`val`-semantik)
- Metoder är pure — `this` är read-only
- Konstruktorer har ingen logik
- Equality är strukturell

### 6.2 Interface (Summatyper / Shapes)

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

**Regler:**
- Interface har inga fält
- Interface har inga metoder med kropp
- Ingen default-implementation
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

### 6.5 Funktioner

```daitai
function distance(a: Vec2, b: Vec2) -> Float:
    val dx: Float = b.x - a.x
    val dy: Float = b.y - a.y
    return sqrt(dx * dx + dy * dy)
```

**Regler:**
- Pure — inga sidoeffekter
- Inga globala variabler
- Inga closures (v1.0) — closures tillåtna i v1.1 med restriktioner

### 6.6 Moduler

```daitai
module Geometry:
    class Vec2:
        x: Float
        y: Float
    function dot(a: Vec2, b: Vec2) -> Float:
        return a.x * b.x + a.y * b.y
```

---

## 7. Kontrollflöde

### 7.1 if/else (uttryck)

```daitai
val result: Int = if x > 0: x else: -x
```

### 7.2 for

```daitai
for item in list:
    process(item)
```

### 7.3 while (restriktivt)

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

Match måste vara uttömmande — alla varianter täcks.

### 7.5 Förbjudet kontrollflöde

| Konstruktion | Status |
|-------------|--------|
| `break` | ❌ Förbjudet |
| `continue` | ❌ Förbjudet |
| `goto` | ❌ Förbjudet |
| `throw` / `try` / `catch` | ❌ Förbjudet |

---

## 8. Felhantering

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

## 9. Algebraiska Strukturer (v1.1)

daitai-language v1.1 har inbyggt stöd för algebraiska strukturer från abstrakt algebra och kategoriteori.

### 9.1 Algebra

| Struktur | Nyckelord | Operationer |
|----------|-----------|-------------|
| Monoid | `MONOID` | `combine`, `empty` |
| Group | `GROUP` | `combine`, `empty`, `inverse` |
| Ring | `RING` | `add`, `mul`, `zero`, `one`, `negate` |
| Semiring | `SEMIRING` | `add`, `mul`, `zero`, `one` |
| Lattice | `LATTICE` | `join`, `meet`, `top`, `bottom` |
| Galois Connection | `GALOIS` | `lower`, `upper` med adjunktionslagar |

### 9.2 Kategoriteori — Grundläggande

| Struktur | Nyckelord | Operationer |
|----------|-----------|-------------|
| Category | `CATEGORY` | `compose`, `identity` |
| Functor | `FUNCTOR` | `map` (behåller komposition/identitet) |
| Natural Transform | `NATURALTRANSFORM` | `transform` (naturlighetskondition) |
| Applicative | `APPLICATIVE` | `pure`, `apply` |
| Monad | `MONAD` | `pure`, `flatMap` (+ monadlagar) |
| Comonad | `COMONAD` | `extract`, `extend` |
| Free Monad | `FREEMONAD` | `pure`, `liftF`, `foldMap` |

### 9.3 Kategoriteori — Avancerat

| Struktur | Nyckelord | Operationer |
|----------|-----------|-------------|
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

### 9.4 Kategoriteori — Monoidal & Högre

| Struktur | Nyckelord | Operationer |
|----------|-----------|-------------|
| Monoidal Category | `MONOIDALCATEGORY` | `tensor`, `unit`, `assoc`, `leftUnit`, `rightUnit` |
| String Diagram | `STRINGDIAGRAM` | `compose`, `tensor`, `identity`, `braid` |
| Enriched Category | `ENRICHEDCATEGORY` | `hom`, `compose`, `identity`, `tensor` |
| Traced Monoidal | `TRACEDMONOIDAL` | `trace`, `loop`, `yanking` |
| 2-Category | `TWOCATEGORY` | `vcomp`, `hcomp`, `identity2`, `whiskerL`, `whiskerR` |
| Compact Closed | `COMPACTCLOSED` | `dual`, `eval`, `coeval`, `name`, `coname` |

### 9.5 Övriga

| Struktur | Nyckelord | Operationer |
|----------|-----------|-------------|
| Topos | `TOPOS` | `subobject`, `pullback`, `omega`, `classify` |
| Operad | `OPERAD` | `compose`, `identity`, `action` |
| F-Algebra | `FALGEBRA` | `algebra`, `carrier`, `cata` |
| Coalgebra | `COALGEBRA` | `unfold`, `observe` |

### 9.6 Exempelsyntax

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

## 10. Transpileringstargets

daitai-language transpilerar till:

| Target | Format |
|--------|--------|
| Pseudo | Läsbar pseudokod |
| TypeScript | ES2020+ moduler |
| Rust | Structs + traits + impl |
| Go | Structs + receiver methods |
| WASM | WAT (WebAssembly Text Format) |
| Java | Classes + interfaces |
| C++ | Structs + namespaces |
| Python | Dataclasses + type hints |

---

## 11. Förbjudna Konstruktioner

| Konstruktion | Status | Alternativ |
|-------------|--------|------------|
| Mutation | ❌ | Ny kopia |
| Arv med beteende | ❌ | Komposition, traits |
| Virtual dispatch | ❌ | Pattern matching |
| Exceptions | ❌ | `Result<T, E>` |
| Global state | ❌ | Explicit state-passing |
| IO | ❌ | IO boundary / PAL |
| Reflection | ❌ | Statiska typer |
| Macros | ❌ | Funktioner |
| `null` | ❌ | `Optional<T>` |
| Implicit typing | ❌ | Explicita typer |
| `break` / `continue` | ❌ | Rekursion / map / filter |

---

## 12. Formell Grammatik (EBNF)

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

## 13. Semantiska Regler

### 13.1 Scope & Symboltabell
- Varje block introducerar ny scope
- Shadowing är förbjudet
- Alla namn måste deklareras före användning

### 13.2 Typkontroll
- Alla uttryck har statisk typ
- Typvariabler unifieras vid instansiering
- Ingen implicit typkonvertering

### 13.3 Purity Check
- Funktioner och metoder får inte mutera state
- Inga sidoeffekter (IO, logging, random)
- `this` är read-only i metoder

### 13.4 Kontrollflödesvalidering
- Alla grenar i if/else måste ha samma returtyp
- Match måste vara uttömmande
- Inga oåtkomliga satser

### 13.5 Algebraiska Lagar
Transpileraren BORDE verifiera att algebraiska deklarationer uppfyller sina lagar
(associativitet, identitet, etc.) men detta är icke-normativt i v1.1.

---

## 14. Versionshistorik

| Version | Datum | Ändringar |
|---------|-------|-----------|
| v1.0 | 2025-12-24 | Initial specifikation |
| v1.1 | 2026-03-28 | Regex-literaler, ADTs med data, match/pattern matching, 30+ algebraiska strukturer (kategoriteori), closures (restriktivt), EBNF-grammatik, formella semantiska regler |
| v1.2 | 2026-03-28 | +DOUBLECATEGORY, +MULTICATEGORY, +MOE, +MULTIMODAL. Totalt 36 algebraiska nyckelord. GPT-2/3/4/5 transformer-specifikationer som validering. |
| v1.3 | 2026-03-28 | +INFINITYGROUPOID (∞-gruppoid för anti-forgetting minne), +SHEAF (lokal→global koherens), +SPLITCOALGEBRA (neuron-split under stress med Topos Ω-verifiering). Totalt 39 algebraiska nyckelord. GPT-7-specifikation som validering. |
| v1.4 | 2026-03-31 | +KET, +BRA, +OPERATOR, +COMPLEX, +HILBERTSPACE, +OBSERVABLE, +MEASUREMENT, +UNITARYGROUP, +DENSITYMATRIX, +QUANTUMCHANNEL. Nativ Dirac bra-ket notation. Kvantgrindar. Kvant+Topos-koppling. Totalt 49 algebraiska nyckelord. |

---

## 15. Algebraiska Strukturer — Referens (v1.2)

### 15.1 Grundläggande algebra

| Nyckelord | Operationer | Lagar |
|-----------|------------|-------|
| `MONOID` | `empty`, `combine(a,b)` | Associativitet, identitet |
| `GROUP` | + `inverse(a)` | + Inverselement |
| `RING` | `add` (grupp), `mul` (monoid) | Distributivitet |
| `SEMIRING` | `add` (monoid), `mul` (monoid) | Distributivitet, annihilation |
| `LATTICE` | `meet(a,b)`, `join(a,b)` | Associativitet, kommutativitet, absorption |

### 15.2 Kategoriteori

| Nyckelord | Operationer | Lagar |
|-----------|------------|-------|
| `CATEGORY` | `identity(a)`, `compose(f,g)` | Associativitet, identitet |
| `FUNCTOR` | `map(f, fa)` | Bevarar identitet och komposition |
| `NATURALTRANSFORM` | `component(a)` | Naturlighetsvillkor (kommutativt diagram) |
| `MONOIDALCATEGORY` | `tensor(f,g)`, `unit`, `associator`, `leftUnitor`, `rightUnitor` | Pentagon, triangel |
| `ENRICHEDCATEGORY` | `hom(a,b)`, `compose`, `identity`, `tensor` | Berikad komposition |
| `TRACEDMONOIDAL` | `trace(f)`, `loop(f,init)`, `yanking` | Naturlighet, yanking, superposition |
| `TWOCATEGORY` | `vCompose`, `hCompose`, `identity2`, `whiskerLeft/Right` | Exchange law |
| `COMPACTCLOSED` | `dual(a)`, `eval`, `coeval`, `name`, `coname` | Dualitetsaxiom |
| `DOUBLECATEGORY` | `hCompose`, `vCompose`, `hIdentity`, `vIdentity`, `square` | Exchange law |
| `STRINGDIAGRAM` | `compose`, `tensor`, `identity`, `braid` | Monoidal koherens |

### 15.3 Högre abstraktion

| Nyckelord | Operationer | Lagar |
|-----------|------------|-------|
| `APPLICATIVE` | `pure(a)`, `apply(ff, fa)` | Identitet, komposition, homomorfism, interchange |
| `MONAD` | `unit(a)`, `flatMap(fa, f)` | Vänster/höger identitet, associativitet |
| `COMONAD` | `extract(w)`, `extend(f,w)`, `duplicate(w)` | Dualt till monad |
| `FREEMONAD` | `pure(a)`, `liftF(fa)`, `foldFree(nat,fm)` | Fri konstruktion |
| `ARROW` | `arr(f)`, `first(af)`, `compose(f,g)` | Arrow-lagar |
| `ARROWCHOICE` | `left(af)`, `right(af)`, `fanin(f,g)` | Val-komposition |
| `ARROWLOOP` | `loop(af)` | Fixpunktssemantik |
| `PROFUNCTOR` | `dimap(f,g,p)`, `lmap(f,p)`, `rmap(g,p)` | Kontravariant/kovariant |
| `BIFUNCTOR` | `bimap(f,g,p)`, `first(f,p)`, `second(g,p)` | Bivariant |
| `CONTRAVARIANT` | `contramap(f,p)` | Omvänd kovarians |

### 15.4 Universella konstruktioner

| Nyckelord | Operationer | Lagar |
|-----------|------------|-------|
| `YONEDA` | `embed(fa)`, `unembed(nat)` | Yoneda-lemmat |
| `LEFTKAN` | `extend(fa)`, `counit(ga)` | Universell egenskap |
| `RIGHTKAN` | `lift(fa)`, `unit(ga)` | Universell egenskap |
| `ADJUNCTION` | `unit(a)`, `counit(fa)`, `leftAdjunct`, `rightAdjunct` | Triangelidentiteter |
| `GALOIS` | `alpha(a)`, `gamma(b)`, `floor(b)`, `ceil(a)` | Monotonicitet, α ⊣ γ |

### 15.5 Algebraisk dataanalys

| Nyckelord | Operationer | Lagar |
|-----------|------------|-------|
| `COALGEBRA` | `unfold(seed)`, `ana(coalg,seed)`, `observe(state)` | Productiv corecursion |
| `FALGEBRA` | `algebra(fa)`, `cata(alg,fix)` | Initialitet, universell fold |
| `OPERAD` | `identity(a)`, `compose(f,gs)`, `arity(f)`, `symmetry(f,perm)` | Associativitet, Σ_n-ekvivarians |
| `MULTICATEGORY` | `identity(a)`, `compose(f,gs)`, `arity(f)`, `cut(f,i,g)` | Associativitet, identitet |
| `TOPOS` | `terminal(a)`, `pullback(f,g)`, `classifier(mono)`, `power(a)` | Subobject classifier, limits |

### 15.6 Domänspecifika (v1.2)

| Nyckelord | Operationer | Lagar |
|-----------|------------|-------|
| `MOE` | `expert(x)`, `gate(x)`, `combine(outputs,weights)`, `route(x)`, `balance(loads)` | Σ_N expert-permutationsinvarians, sparsity, kapacitetsbalansering |
| `MULTIMODAL` | `encode(modality,x)`, `fuse(reprs)`, `align(a,b)`, `project(x,target)` + `modalities:` | Koherens (fusion kommuterar med modalitetstransformationer), naturlighet |

### 15.7 Högre homotopiteori & dynamisk tillväxt (v1.3)

| Nyckelord | Operationer | Lagar |
|-----------|------------|-------|
| `INFINITYGROUPOID` | `cell(level,a,b)`, `compose(p,q)`, `inverse(p)`, `identity(a)`, `coherence(level)` | Alla morfismer inverterbara, path-connectivity (∀a,b: ∃p: cell(0,a,b)), högre koherens på varje nivå |
| `SHEAF` | `section(U)`, `restrict(s,V)`, `glue(sections)`, `isCompatible(sections)` | Lokalitet (sektioner lika på överlapp → globalt lika), Limning (kompatibla sektioner → global sektion), Funktorialitet (restrict∘restrict = restrict) |
| `SPLITCOALGEBRA` | `observe(state)`, `split(neuron,stress)`, `merge(a,b)`, `stress(neuron)`, `verify(pre,post)` | Semantikbevarande (verify(pre,post) ⟹ meaning(pre) ≅ meaning(post)), Stabilitet (post-split stress < threshold), Coalgebrisk (observe = S → F(S)) |

**∞-Groupoid** — Grunden för HoTT (Homotopy Type Theory). Typer *är* ∞-gruppoider. Användning: anti-forgetting minne där kunskap lever i ett path-connected rum — inget kan bli otillgängligt.

**Sheaf** — Limningsteorin: lokalt konsistent data kan alltid lyftas till globalt konsistent data. Användning: MoE-koherens där varje expert har lokal kunskap som måste integrera till en konsistent helhet. H¹(Sheaf) = 0 ⟹ inga konflikter.

**SplitCoalgebra** — Coalgebrisk neuron-split: neuroner delar sig under stress (rate² > threshold) och verifieras via Topos subobject classifier Ω. Användning: dynamisk arkitekturtillväxt utan semantikförlust.

---

## 16. Kvantmekanisk algebra (v1.4)

### 16.1 Hilbert-rums-sorter

v1.4 introducerar nativa Dirac-notationsliteraler och algebraiska sorter för kvantberäkning:

```
Ket        -- |ψ⟩  (kolumnvektor i Hilbert-rummet)
Bra        -- ⟨φ|  (radvektor, adjungerad av Ket)
Operator   -- linjär operator A: H → H
Complex    -- komplext tal a + bi
```

### 16.2 Kvant-literaler

```
|0⟩, |1⟩, |+⟩, |−⟩       -- Standard kvantbitar
|ψ⟩ = α|0⟩ + β|1⟩        -- Superposition
⟨φ| = ⟨0|γ* + ⟨1|δ*      -- Bra (konjugerad transponat)
⟨φ|ψ⟩                     -- Inre produkt (skalärt tal ∈ Complex)
|ψ⟩⟨φ|                    -- Yttre produkt (Operator)
```

### 16.3 Nyckelord

```
KET           BRA           OPERATOR      COMPLEX
HILBERTSPACE  OBSERVABLE    MEASUREMENT
UNITARYGROUP  DENSITYMATRIX QUANTUMCHANNEL
```

### 16.4 Algebraiska strukturer

| Nyckelord | Operationer | Lagar |
|-----------|------------|-------|
| `KET` | `add(a,b)`, `scale(α,v)`, `norm(v)`, `inner(u,v)`, `tensor(u,v)` | Linjäritet, ⟨ψ\|ψ⟩ ≥ 0, ‖ψ‖ = 1 (normalisering) |
| `BRA` | `adjoint(ket)`, `inner(bra,ket)`, `scale(α,b)` | ⟨φ\| = (|φ⟩)†, anti-linjäritet i första argumentet |
| `OPERATOR` | `apply(A,ψ)`, `compose(A,B)`, `adjoint(A)`, `commutator(A,B)`, `tensor(A,B)` | Linjäritet, (AB)† = B†A†, [A,B] = AB - BA |
| `HILBERTSPACE` | `dim(H)`, `basis(H)`, `project(ψ,subspace)`, `directSum(H1,H2)`, `tensorProduct(H1,H2)` | Fullständighet, separabilitet, inre produkts positivitet |
| `OBSERVABLE` | `eigenvalues(O)`, `eigenstates(O)`, `expectation(O,ψ)`, `uncertainty(O,ψ)` | Hermitiskhet (O = O†), spektralteoremet |
| `MEASUREMENT` | `measure(ψ,basis)`, `collapse(ψ,outcome)`, `probability(ψ,outcome)` | Born-regeln (P = |⟨outcome\|ψ⟩|²), Σ P_i = 1 |
| `UNITARYGROUP` | `identity(n)`, `compose(U,V)`, `inverse(U)`, `det(U)` | UU† = I, \|det(U)\| = 1 |
| `DENSITYMATRIX` | `pure(ψ)`, `mixed(states,probs)`, `trace(ρ)`, `purity(ρ)`, `entropy(ρ)` | Tr(ρ) = 1, ρ ≥ 0, hermitisk |
| `QUANTUMCHANNEL` | `apply(E,ρ)`, `kraus(operators)`, `isCP(E)`, `isTP(E)` | Fullständig positivitet, spårbevarande (Σ E_k†E_k = I) |

### 16.5 Dirac-algebra

Följande operatoridentiteter verifieras vid kompilering:

```
⟨φ|ψ⟩ = (⟨ψ|φ⟩)*                    -- Konjugatsymmetri
⟨φ|(α|ψ₁⟩ + β|ψ₂⟩) = α⟨φ|ψ₁⟩ + β⟨φ|ψ₂⟩  -- Linjäritet
(|ψ⟩⟨φ|)† = |φ⟩⟨ψ|                   -- Adjungerad av yttre produkt
Σ_i |i⟩⟨i| = I                       -- Fullständighetsrelation
```

### 16.6 Kvantgrindar (fördefinierade operatorer)

```
PAULIX    = |0⟩⟨1| + |1⟩⟨0|           -- Pauli-X (NOT)
PAULIY    = -i|0⟩⟨1| + i|1⟩⟨0|        -- Pauli-Y
PAULIZ    = |0⟩⟨0| - |1⟩⟨1|           -- Pauli-Z
HADAMARD  = (|0⟩⟨0| + |0⟩⟨1| + |1⟩⟨0| - |1⟩⟨1|) / √2
CNOT      = |00⟩⟨00| + |01⟩⟨01| + |10⟩⟨11| + |11⟩⟨10|
```

**Kvant + Topos koppling:** MEASUREMENT använder Topos subobject classifier Ω för att formellt hantera mätutfall som sanningsvärden i en intuitionistisk logik. Born-regeln uttrycks som en naturlig transformation från DENSITYMATRIX till SHEAF av sannolikhetsfördelningar.

---

## 17. Framtida (v1.5, icke-normativt)

- Higher-Kinded Types
- Effect System (algebraiska effekter)
- Dependent Types (begränsat)
- Linear Types (kvant no-cloning-teoremet)
- Formell verifiering av algebraiska lagar
- ∞-Category (fullständig ∞-kategoriteori)
- Higher Operad (∞-operad)
- Spectral Sequences (homologisk algebra)
- Persistent Homology (topologisk dataanalys)
- Quantum Error Correction (stabilisatorkoder)
- Topological Quantum Computation (anyoner, flätgrupper)
