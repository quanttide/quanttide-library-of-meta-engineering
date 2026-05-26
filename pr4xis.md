# pr4xis

Prove your domain is correct — ontology-driven rule enforcement with category theory, logical composition, and runtime state machines.

## Links

- Repository: https://github.com/i-am-logger/pr4xis
- Crates.io: https://crates.io/crates/pr4xis
- Docs: https://docs.rs/pr4xis

## Overview

pr4xis is a Rust crate that provides:

- **`category`** — Category theory abstractions: Category, Functor, NaturalTransformation, Monad, Comonad, Yoneda, Kleisli, etc.
- **`ontology`** — Define what exists, how things relate, and what rules govern them.
- **`engine`** — Runtime enforcement engine with `Situation`, `Action`, `Precondition`, and `Engine` traits, supporting undo/redo/trace.
- **`logic`** — Logical composition and invariant checking.

## Usage in quanttide-meta

pr4xis serves as the runtime engine for the bounded expansion workflow:

```rust
Engine::new(initial_situation, preconditions, apply_fn)
       .next(action)       // checks preconditions, applies transformation
       .back()             // undo
       .forward()          // redo
```

The `Situation` / `Action` / `Precondition` traits map directly to the categorical model's objects, morphisms, and guards.

## License

CC-BY-NC-SA-4.0
