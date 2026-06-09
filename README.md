<p align="center">
  <img src="https://img.shields.io/badge/version-1.3-blue" alt="Version 1.3">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="MIT License">
  <img src="https://img.shields.io/badge/keywords-39-purple" alt="39 Algebraic Keywords">
  <img src="https://img.shields.io/badge/targets-7-orange" alt="7 Target Languages">
</p>

# daitai-language

> An algebraic pseudolanguage for reasoning, specification, and transpilation.

**Creator:** Joakim Cöster  
**Version:** 1.4 · **Status:** Normative · **Date:** 2026-06-09

---

## What is daitai?

daitai-language is a **formally specified algebraic language** designed to express algorithms with mathematical precision. It is not a runtime language — it exists for **reasoning**, **transformation**, and **transpilation** to:

| Target | Status |
|--------|--------|
| TypeScript / JavaScript | ✅ Full |
| Rust | ✅ Full |
| Go | ✅ Full |
| WebAssembly (WAT/WASM) | ✅ Full |
| Java | ✅ Full  |
| C++ | ✅ Full  |
| Python | ✅ Full  |

## Design Principles

| # | Principle | Description |
|---|-----------|-------------|
| D1 | **Determinism** | All expressions are deterministic and referentially transparent |
| D2 | **Static typing** | Explicit types everywhere, no inference |
| D3 | **Immutability** | All data immutable by default |
| D4 | **Expression-based** | Everything returns a value |
| D5 | **Language neutrality** | No coupling to any target language |
| D6 | **Purity** | All functions are pure |
| D7 | **Composition** | Composition over inheritance, always |

## Quick Example

```
monoid IntSum over Int:
    empty = 0
    combine(a: Int, b: Int) -> Int:
        return a + b

functor ListF over List:
    map(f: (Int) -> Int, xs: List[Int]) -> List[Int]:
        return xs.map(f)

// Pipeline operator
result = data |> transform |> validate |> serialize

// Formal verification
verify IntSum satisfies Monoid:
    associativity: combine(combine(a, b), c) == combine(a, combine(b, c))
    left_identity: combine(empty, a) == a
    right_identity: combine(a, empty) == a
```

## 39 Algebraic Keywords

daitai has **39 first-class algebraic structure keywords** spanning from fundamental algebra to higher homotopy theory:

### Fundamental Algebra
`MONOID` · `GROUP` · `RING` · `SEMIRING` · `LATTICE`

### Category Theory
`CATEGORY` · `FUNCTOR` · `NATURALTRANSFORM` · `MONOIDALCATEGORY` · `ENRICHEDCATEGORY` · `TRACEDMONOIDAL` · `TWOCATEGORY` · `COMPACTCLOSED` · `DOUBLECATEGORY` · `STRINGDIAGRAM`

### Higher Abstraction
`APPLICATIVE` · `MONAD` · `COMONAD` · `FREEMONAD` · `ARROW` · `ARROWCHOICE` · `ARROWLOOP` · `PROFUNCTOR` · `BIFUNCTOR` · `CONTRAVARIANT`

### Universal Constructions
`YONEDA` · `LEFTKAN` · `RIGHTKAN` · `ADJUNCTION` · `GALOIS`

### Algebraic Data Analysis
`COALGEBRA` · `FALGEBRA` · `OPERAD` · `MULTICATEGORY` · `TOPOS`

### Domain-Specific (AI/ML)
`MOE` · `MULTIMODAL`

### Higher Homotopy Theory (v1.3)
`INFINITYGROUPOID` · `SHEAF` · `SPLITCOALGEBRA`

## Formal Verification

daitai includes a built-in **property-based verification system** for algebraic laws. The compiler can verify that your algebraic structures satisfy their mathematical axioms:

```
verify MyMonoid satisfies Monoid:
    samples = 1000
    associativity: combine(combine(a, b), c) == combine(a, combine(b, c))
    left_identity: combine(empty, a) == a
```

The verifier generates random test values and checks each law over N samples, reporting counterexamples on failure. It can also **transpile verification to TypeScript** for integration with standard test runners.

**Supported structures:** Monoid, Group, Ring, Semiring, Lattice, Category, Functor, Monad.

## Module System (v1.4)

```
module Core:
    export function identity(x: Int) -> Int:
        return x

import { identity } from "Core"

// Pipeline composition
result = input |> identity |> transform
```

## WebAssembly Backend

The compiler generates **type-aware WebAssembly** with:

- ✅ vtable-based interface dispatch via `call_indirect` + `br_table`
- ✅ Self-recursive and mutual tail-call optimization (trampoline)
- ✅ Mark-sweep-compact GC with string interning
- ✅ Optional, Result, Map runtime
- ✅ WASI preview1 support (fd_write, proc_exit, clock_time_get)

## File Extensions

| Extension | Description |
|-----------|-------------|
| `.dai` | Standard daitai source |
| `.daitai` | Full extension |
| `.vcl.dai` | Visual Component Library |
| `.kashika.dai` | Presentation/slides |
| `.ddf.dai` | Document format |
| `.chart.dai` | Chart definitions |
| `.ui.dai` | UI component specs |
| `.swarm.dai` | Swarm intelligence |

## Specification Documents

| Document | Language |
|----------|----------|
| [daitai-language-v1.3.md](docs/daitai-language-v1.3.md) | 🇸🇪 Svenska |
| [daitai-language-v1.3.en.md](docs/daitai-language-v1.3.en.md) | 🇬🇧 English |
| [daitai-language-v1.3.ja.md](docs/daitai-language-v1.3.ja.md) | 🇯🇵 日本語 |

## VS Code Extension

A TextMate grammar for syntax highlighting is available in [`docs/vscode-daitai/`](docs/vscode-daitai/).

## Architecture

```
daitai-language
├── Specification (this repo)
│   ├── Language spec v1.3
│   ├── TextMate grammar
│   └── EBNF grammar
├── Compiler (daitai-compiler)
│   ├── Parser (indent-based + brace)
│   ├── AST (algebraic sum types)
│   ├── Generator → daitai / Rust / Go / TypeScript
│   ├── WASM backend (WAT → binary)
│   ├── Formal verifier (property-based)
│   └── WASI support
└── Algebraic Foundation (daitai-algebra)
    ├── Category theory
    ├── Homotopy type theory
    └── Topological guarantees
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| v1.0 | 2026-03-20 | Initial specification |
| v1.1 | 2026-03-28 | Regex literals, ADTs, match/pattern matching, 30+ algebraic structures, closures, EBNF grammar |
| v1.2 | 2026-03-28 | +DOUBLECATEGORY, +MULTICATEGORY, +MOE, +MULTIMODAL. 36 algebraic keywords |
| v1.3 | 2026-03-28 | +INFINITYGROUPOID, +SHEAF, +SPLITCOALGEBRA. 39 keywords. Module system, pipeline operator, formal verification |

## License

MIT © Joakim Cöster

---

<p align="center">
  <em>Clarity, determinism, and correctness are strictly more important than expressiveness or convenience.</em>
</p>
