# pr4xis

证明你的领域是正确的 —— 基于本体论的规则强制，结合范畴论、逻辑组合和运行时状态机。

`pr4xis` 是一个用 Rust 编写的、以本体论驱动和范畴论为核心的推理引擎。它并非基于统计模式，而是**从形式化的公理出发进行推导**，每一个结论都能追溯到其证明路径。

## 链接

- 仓库：https://github.com/i-am-logger/pr4xis
- Crates.io：https://crates.io/crates/pr4xis
- 文档：https://docs.rs/pr4xis
- 在线体验：https://pr4xis.dev

## 🧠 核心理念与定位

`pr4xis` 的核心理念是「**可检查的推理**」（reasoning you can check）。它旨在为那些需要**正确性**而非仅仅是**合理性**的系统提供支持，这与主流的大语言模型（LLM）形成鲜明对比。

- **确定性推理**：LLM 预测下一个 token，而 `pr4xis` 推导下一个 claim。它不会产生幻觉，因为每个结论都是通过公理演绎得出的。
- **透明可追溯**：当结论不成立时，引擎会明确指出是**哪条公理失败**，而非像 LLM 那样难以定位错误。所有结论都附带完整的证明路径。
- **作为 LLM 的补充**：一个自然的应用模式是让 LLM 负责生成，而 `pr4xis` 在后台负责**检查哪些主张真正成立**，形成一个既流畅又精确的管道。

## 🏗️ 架构设计：五层可组合栈

`pr4xis` 的架构是一个清晰的五层结构，每一层仅依赖于其下方的层。领域知识被封装在可组合的**本体论（Ontologies）**中，而非硬编码的处理逻辑里。

| 层级 | 模块 | 核心职责 |
|:--|:--|:--|
| **逻辑层** | `pr4xis::logic` | 提供命题逻辑基础，包括公理、命题、逻辑组合（`AllOf`, `AnyOf`, `Not`）以及演绎、归纳、溯因三种推理模式。 |
| **范畴层** | `pr4xis::category` | 范畴论原语，如实体、关系、函子、自然变换、伴随，以及 `Writer` 单子、`Lens` 等代数结构。范畴与函子定律作为一阶 `Axiom` 实现，并通过属性测试进行验证。 |
| **本体层** | `pr4xis::ontology` | 定义「事物是什么」以及它们之间的关系。通过 `ontology!` 过程宏声明概念、标签和带类型（kinded）的边（如 `is_a`, `has_a`, `causes`），宏会自动生成 `Concept` 枚举、`Category` 实现等。 |
| **引擎层** | `engine` | 运行时规则执行。它根据加载的本体论验证状态转换，并明确指出阻塞转换的精确规则。 |
| **代码生成层** | `codegen` | 构建时代码生成。它将编译好的本体论（`Category`）桥接到 `Archive`，支持声明式的本体论数据交付。 |

## 🚀 主要功能与特性

- **可验证的归档（`.prx` 文件）**：`pr4xis` 可以将加载的数据源打包成一个紧凑、自包含的 `.prx` 文件。读取时会先检查归档的指纹，拒绝任何被篡改的内容。该文件可以在毫秒内读回，并且能够**逐字节地重建**原始源。
- **超过 160 个领域本体论**：工作区内置了超过 160 个领域本体论，覆盖了生物医学、传感器融合、导航、感知、太空、水下、工业、语言学、形式数学、音乐、色彩、司法工作流等多个领域。
- **跨领域函子（Cross-domain Functors）**：不同领域的本体论可以通过跨领域函子进行组合，这些函子的定律在测试时通过 `assert_functor_laws` 进行检查，确保组合的数学正确性。
- **状态机引擎**：提供了一个端到端的状态机引擎，用于定义情境、动作和前置条件。它根据加载的本体论验证状态转换，是构建需要严格规则约束的应用（如对话引擎、HTTP 状态机）的基础。
- **可复现的测试结果**：项目强调测试验证，例如它包含一个关于生物电学的**间隙检测（gap-detection）**发现，任何人都可以重新运行并验证。

## 📦 使用方式与获取

`pr4xis` 是一个**库（library）**，而非服务。它没有守护进程，也没有 GPU 或 API 密钥需求，可以直接作为 Cargo 依赖项嵌入到你的 Rust 项目中。

基本使用步骤：

1. **添加依赖**：在你的 `Cargo.toml` 中添加 `pr4xis` 或 `pr4xis-domains` 作为依赖。
2. **定义本体**：使用 `ontology!` 宏来声明你领域内的概念和关系。
3. **调用引擎**：在你的代码中直接调用 `pr4xis` 引擎，进行推理、状态验证或查询。
4. **验证与构建**：运行 `cargo test --workspace` 来验证所有范畴和函子定律。你也可以克隆仓库进行本地构建。

**在线体验**：访问 [pr4xis.dev](https://pr4xis.dev) 可直接在浏览器中试用，完全在浏览器中运行，无需服务器或 API 密钥。

## 在 quanttide-meta 中的使用

pr4xis 充当有界展开工作流的运行时引擎：

```rust
Engine::new(initial_situation, preconditions, apply_fn)
       .next(action)       // 检查前置条件，应用状态变换
       .back()             // 撤销
       .forward()          // 重做
```

`Situation` / `Action` / `Precondition` 三个 trait 直接对应到范畴模型的对象、态射和守卫。

## 替代方案

直接替代（同样做「范畴论 + 本体 + Rust」）的库很少，但按功能拆开，每一块都有替代方案。pr4xis 的独特之处在于组合：用 Rust 类型系统把范畴论结构（Category / Functor / Natural Transformation）编译进去，让本体之间的映射和整合有证明可查。这个组合——范畴论形式化 + Rust 编译期校验 + 多域本体组合——在开源生态里没有明显的直接对等物。

### 范畴论基础设施

如果只需要范畴论的 Rust 原语（范畴、函子、自然变换、伴随、Yoneda），而不需要完整本体引擎：

| 库 | 定位 |
|:--|:--|
| algar | Rust 范畴论实现，作者定位是学习/探索性质，实现了范畴、函子、自然变换等基本结构 |
| lau-category-theory | 抽象范畴论 Rust 实现，覆盖 functor、adjunction、limits/colimits、monad、Yoneda，并附 agent-protocol 组合应用示例 |
| karpal_topos | 拓扑斯论构造：小范畴、预层（presheaf）、可表示预层、筛、Yoneda 引理，面向「结构化空性」的代数基础设施 |
| symthaea_core | 纯 Rust 实现的抽象范畴、函子、自然变换、伴随、monad、极限/余极限、Yoneda 嵌入，不依赖外部 crate |
| csw-core | 「Categorical Semantics Workbench」的核心范畴结构，用于定义范畴并推导类型系统 |

这些库提供范畴论的计算抽象，但不绑定本体格式，也不做跨域映射证明。

### 本体推理

如果要的是 OWL/RDF 本体推理，Rust 生态里有更成熟的专用库：

| 库 | 能力 |
|:--|:--|
| reasonable | OWL 2 RL reasoner，有 Python 绑定（pyreasonable），输出 base + inferred 三元组 |
| OntoLogos | 模块化 Rust 本体 reasoner，支持 OWL EL、OWL RL、RDFS，含解释生成和增量分类 |
| rustdl | OWL 2 DL（SROIQ）reasoner，原生 Rust，无 JVM、无子进程，有 Python 绑定 |

这些是标准本体语言（OWL/RDF）的推理器，走描述逻辑路线而非范畴论路线。如果「替代」指「能推理就行」，它们是成熟选项；如果要「用函子证明跨域映射正确」，它们不覆盖。

### 知识图谱与语义图

| 库 | 定位 |
|:--|:--|
| quipu-ai | AI-native 知识图谱，带严格本体强制（ontology enforcement） |
| lattix | 知识图谱基底：三元组、异质图、中心性/社区算法、RDF 格式支持 |
| rowl | Dolfin Ontology Language 解析器 |

这些库处理图结构与格式解析，不涉及范畴论的形式化层。

### 理论传统

pr4xis 自述受 Spivak 的 Ologs 影响。Ologs（ontology logs）是 Spivak 和 Kent 提出的范畴论知识表示框架，用范畴论的图表来表达本体，是 pr4xis 的理论前身，但不是软件替代品——它是数学框架，没有对应的 Rust 实现。IFF（Information Flow Framework）有专门的 Category Theory Ontology（IFF-CAT）作为元本体，是范畴论本体的标准参考，同样不是可运行的软件库。

### 替代边界

| 替代的是哪一块 | 有成熟替代 | 代表 |
|:--|:--|:--|
| 范畴论 Rust 原语 | 有 | algar、lau-category-theory、karpal_topos、symthaea_core |
| OWL/RDF 本体推理 | 有 | reasonable、OntoLogos、rustdl |
| 知识图谱装载/图算法 | 有 | quipu-ai、lattix |
| 范畴论形式化 + 本体组合 + Rust 编译期证明 | 无直接对等物 | pr4xis 自称是首个此类可执行基底 |
| Ologs / IFF-CAT 理论框架 | 有理论，无软件 | Spivak Ologs、IFF-CAT |

只替代其中某一层，有现成选项；要 pr4xis 那种「范畴论当基底、函子当映射、编译期校验当证明」的完整组合，目前没有开箱即用的替代品。最接近的理论路线是 Ologs 加自己的范畴论实现，但那意味着自己补上 pr4xis 已经做掉的工程。

## 许可证

CC-BY-NC-SA-4.0
