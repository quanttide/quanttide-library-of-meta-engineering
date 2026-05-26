# pr4xis

证明你的领域是正确的 —— 基于本体论的规则强制，结合范畴论、逻辑组合和运行时状态机。

## 链接

- 仓库：https://github.com/i-am-logger/pr4xis
- Crates.io：https://crates.io/crates/pr4xis
- 文档：https://docs.rs/pr4xis

## 概述

pr4xis 是一个 Rust crate，提供：

- **`category`** — 范畴论抽象：Category、Functor、NaturalTransformation、Monad、Comonad、Yoneda、Kleisli 等
- **`ontology`** — 定义存在什么、事物如何关联、受什么规则约束
- **`engine`** — 运行时强制引擎，提供 `Situation`、`Action`、`Precondition`、`Engine` trait，支持撤销/重做/追踪
- **`logic`** — 逻辑组合与不变式检查

## 在 quanttide-meta 中的使用

pr4xis 充当有界展开工作流的运行时引擎：

```rust
Engine::new(initial_situation, preconditions, apply_fn)
       .next(action)       // 检查前置条件，应用状态变换
       .back()             // 撤销
       .forward()          // 重做
```

`Situation` / `Action` / `Precondition` 三个 trait 直接对应到范畴模型的对象、态射和守卫。

## 许可证

CC-BY-NC-SA-4.0
