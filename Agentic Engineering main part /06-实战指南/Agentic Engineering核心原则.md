# Agentic Engineering 核心原则

> **核心来源：**
> - Anthropic《Building Effective Agents》三条设计原则
> - Addy Osmani《Agentic Engineering》（2026 年 2 月 4 日）
> - Simon Willison《Agentic Engineering Patterns》全部章节（2026 年 2 月-3 月）
> - Dan Shipper & Kieran Klaassen at Every《Compound Engineering》

---

## 一、来自 Anthropic 的三条设计原则

来源：Anthropic《Building Effective Agents》

### 原则一：简洁性（Simplicity）

> "Maintain simplicity in your agent's design."

- 从最简单的方案开始——一个 prompt + 工具定义
- 仅在更简单的方案确实不足时才增加复杂度
- 避免为了使用框架而使用框架
- 不要引入 Agent 框架，除非你确定直接用 API 不够

Anthropic 的完整表述：
> "Start with simple prompts. You can do a lot with a good prompt and a well-defined tool interface. Add complexity only when you need it."

这条原则在本文系统的 05-工具生态/主流Agent框架.md 中有详细展开：Anthropic 明确警告框架"often create extra layers of abstraction that can obscure the underlying prompts and responses, making them harder to debug"。

### 原则二：透明性（Transparency）

> "Prioritize transparency by explicitly showing the agent's planning steps."

- 代理的规划步骤应该可见——开发者需要知道代理"在想什么"
- 使用追踪（Tracing）记录代理的决策过程
- 保持对"底层发生了什么"的清晰理解
- 避免黑盒式的代理行为

Claude Code 的实践：通过 OpenTelemetry 导出详细的追踪数据，包括每个 API 请求、每个工具调用、每个权限决策。

### 原则三：ACI 设计（Agent-Computer Interface）

> "Carefully craft your agent-computer interface (ACI) through thorough tool documentation and testing."

- 投入与 HCI（人机交互）同等的精力来设计 ACI（代理-计算机交互）
- 像为初级开发者写文档一样为代理设计工具接口
- 测试代理如何使用你的工具——代理的使用方式可能与你的预期不同
- 工具描述应该精确、无歧义、包含使用示例

---

## 二、来自 Addy Osmani 的核心理念

来源：Addy Osmani《Agentic Engineering》（2026 年 2 月 4 日）

### 原则四：先规划，再编码

> "Before prompting anything, you write a design doc or spec."

- 写设计文档或规格说明（可以使用 AI 辅助，但必须由你主导）
- 将工作分解为明确定义的任务
- 确定架构方案，而不是让代理自行决定
- 规格说明是你审查代理输出的标准——没有标准就无法审查

### 原则五：审查每一行代码

> "You review that code with the same rigor you'd apply to a human teammate's PR."

- 以审查人类同事 PR 的严格程度审查代理输出
- 如果无法解释一个模块的功能，则不允许合入
- 你必须对你的代码负责——不管它是谁写的

### 原则六：测试是一切的基础

> "The single biggest differentiator between agentic engineering and vibe coding is testing."

- 拥有可靠的测试套件是代理高效迭代的前提
- 测试是将不可靠的代理转化为可靠系统的关键
- TDD（测试驱动开发）与代理特别配合——先写测试，再让代理实现

### 原则七：拥有代码库

- 维护文档
- 使用版本控制和 CI
- 监控生产环境
- **AI 加速工作，但开发者对系统负责**

### 原则八：AI 奖励良好实践

> "The better your specs, the better the AI's output. The more comprehensive your tests, the more confidently you can delegate. The cleaner your architecture, the less the AI hallucinates weird abstractions."

这是一个反直觉但极具洞察力的观点：**AI 辅助开发实际上比传统编码更需要良好的工程实践。** 传统开发中，差的架构和缺失的测试只是让你自己更痛苦；在 AI 辅助开发中，它们会让代理产生更差的输出。AI 不是绕过工程实践的工具，而是放大工程实践效果的工具——好的实践被放大成更好的结果，差的实践被放大成更差的结果。

### 原则九：警惕技能退化

> "If you're junior and you lean on AI before building those fundamentals, you risk a dangerous skill atrophy. You can produce code without understanding it. You can ship features without learning why certain patterns exist."

初学者过度依赖 AI 的风险：能生成代码但不能调试，能提交功能但不能推理。AI 应该辅助学习，而非替代学习。

---

## 三、来自 Simon Willison 的核心理念

来源：Simon Willison《Agentic Engineering Patterns》（2026 年 2 月-3 月全部章节）

### 原则十：AI 放大的是现有能力

> "AI tools amplify existing expertise. The more skills and experience you have as a software engineer the faster and better the results you can get from working with LLMs and coding agents."

AI 代理不是替代技能的工具，而是放大技能的工具。一个经验丰富的软件工程师使用编码代理，比一个新手使用同一个代理，产出质量差距巨大——因为前者能写出更好的规格说明、设计更好的测试、做出更好的架构决策，而这些恰恰是代理产出质量的决定因素。

### 原则十一：在顶层发挥

> "If you're going to really exploit the capabilities of these new tools, you need to be operating at the top of your game."

Willison 列出了 Agentic Engineering 需要的 12 项核心能力：

1. 自动化测试
2. 提前规划
3. 全面的文档
4. 良好的版本控制习惯
5. 有效的自动化（CI/CD）
6. 代码审查文化
7. 一种"非常奇怪的管理"能力——管理代理而不是管理人类
8. 优秀的手动 QA
9. 强大的研究技能
10. 部署到预发布环境的能力
11. 直觉判断什么可以外包给 AI
12. 更新后的估算能力——在 AI 加速下重新估算工作

### 原则十二：写代码很便宜了

来源：Willison《Writing code is cheap now》

代码的初始生成成本已经接近零。这个事实颠覆了从宏观到微观的大量工程直觉：

**宏观层面：** 我们花大量时间设计、估算、规划项目，以确保昂贵的编码时间被最高效地利用。现在编码时间几乎免费了。

**微观层面：** "Should I refactor that function to be slightly more elegant if it adds an extra hour of coding time?"——现在这个权衡天平完全倾斜了。

**但是，好的代码仍然有成本：**

Willison 定义了"好代码"的完整标准：
- 代码能工作（does what it's meant to do, without bugs）
- **你知道代码能工作**——你采取了步骤确认代码是合格的
- 解决了正确的问题
- 优雅地处理错误——不只是 happy path
- 简洁且最小——只做需要的事
- 被测试保护
- 有适当的文档
- 设计允许未来变更（但不预支不需要的复杂度）
- 满足相关的"ility"（accessibility, testability, reliability, security, maintainability, observability, scalability, usability）

编码代理可以协助以上大部分，但仍有 substantial burden 落在驱动这些工具的开发者身上。

**核心习惯变化：** 任何时刻你的直觉说"不值得花时间构建那个"——都试试发起一个 prompt 到异步代理会话。最坏的情况是你 10 分钟后检查发现确实不值得那些 Token。

### 原则十三：囤积你熟悉的知识

来源：Willison《Hoard things you know how to do》

> 保持一个你熟悉的技术和方法的"工具箱"。

编码代理使这种"囤积"变得更加有力量，因为你不仅可以自己使用这些知识，还可以快速将它们**重新组合**成新的解决方案。代理的循环执行能力意味着你可以让代理尝试多种技术组合，从中找出最优解。

### 原则十四：AI 应该产出更好的代码

来源：Willison《AI should help us produce better code》

> "Shipping worse code with agents is a choice. We can choose to ship code that is better instead."

如果采用编码代理后代码质量下降了，那不是工具的问题——是你流程的问题。代理使许多过去"太耗时"的质量改进变得几乎免费：
- 重构一个糟糕的 API 设计（涉及数十个文件的修改）
- 统一命名约定
- 合并重复但略有不同的功能
- 拆分过大的文件

Willison 提出了**复合工程循环（Compound Engineering）**的概念，来自 Dan Shipper 和 Kieran Klaassen at Every：

> "Every coding project they complete ends with a retrospective, which they call the compound step where they take what worked and document that for future agent runs."

每个项目结束后的回顾，将经验记录下来供未来的代理运行使用。小改进复合积累。

### 原则十五：探索性原型

Willison 建议用代理做**探索性原型**来辅助技术选型：

> "The best way to make confident technology choices is to prove that they are fit for purpose with a prototype."

代理可以快速构建原型来验证技术方案——例如测试 Redis 在高并发场景下的表现。由于原型成本几乎为零，可以同时测试多个方案。

---

## 四、来自 Willison 的反模式原则

来源：Willison《Anti-patterns: things to avoid》

### 原则十六：不要将未经审查的代码强加给协作者

> "Don't file pull requests with code you haven't reviewed yourself."

这是 Willison 明确列出的**头号反模式**。如果你发起一个包含数百甚至上千行代理生成代码的 PR，而你自己没有验证这些代码能工作——你实质上是在将工作委托给别人。

一个合格的 Agentic Engineering PR 应该具备：
- **代码能工作，你确信它能工作**——"Your job is to deliver code that works"
- **变更足够小，审查者能高效审查**——多个小 PR 优于一个大 PR
- **包含额外上下文**——变更服务于什么更高层目标？关联 issue 或规格说明
- **包含你人工验证的证据**——手动测试笔记、实现选择的说明、功能运行的截图/视频

特别警告：**代理会生成令人信服但可能不准确的 PR 描述——你需要审查这些描述。**

---

## 十七、来自 Osmani 的 AGENTS.md 原则

来源：Osmani《Stop Using /init for AGENTS.md》

### 原则十七：AGENTS.md 是诊断工具，不是配置文件

> "Think of AGENTS.md as a living document of friction you haven't fixed yet."

- 你加的每一行都代表代码库中某些令人困惑的东西
- 正确的回应不是增长上下文文件，而是修复底层问题
- 代理反复犯同一个错误？修复代码库结构，而不是加一条规则
- 代理遗忘运行类型检查？在构建流水线中自动执行，而不是用散文指令

---

## 十八、原则汇总与交叉引用

| # | 原则 | 来源 | 与 Vibe Coding 的区别 |
|---|------|------|----------------------|
| 1 | 简洁性 | Anthropic | Vibe Coding 跳过规划直接编码；Agentic Engineering 从最简方案逐步增加 |
| 2 | 透明性 | Anthropic | Vibe Coding 不关心代理内部决策过程；Agentic Engineering 要求追踪和审查 |
| 3 | ACI 设计 | Anthropic | Vibe Coding 将代理视为黑盒；Agentic Engineering 精心设计代理的工具接口 |
| 4 | 先规划再编码 | Osmani | Vibe Coding 直接开始；Agentic Engineering 先写规格说明 |
| 5 | 审查每一行代码 | Osmani | Vibe Coding 盲目接受输出；Agentic Engineering 以人类 PR 标准审查 |
| 6 | 测试是一切的基础 | Osmani | **这是最大的区别**——Vibe Coding 没有测试；Agentic Engineering 以测试驱动 |
| 7 | 拥有代码库 | Osmani | Vibe Coding 不关心维护；Agentic Engineering 要求完整的工程实践 |
| 8 | AI 奖励良好实践 | Osmani | Vibe Coding 放大差的实践；Agentic Engineering 放大好的实践 |
| 9 | 警惕技能退化 | Osmani | Vibe Coding 助长退化；Agentic Engineering 要求基础优先 |
| 10 | AI 放大现有能力 | Willison | Vibe Coding 试图替代技能；Agentic Engineering 要求在技能基础上使用 AI |
| 11 | 在顶层发挥 | Willison | Vibe Coding 降低标准；Agentic Engineering 提高标准 |
| 12 | 写代码很便宜了 | Willison | 两者的共同认知，但 Agentic Engineering 更强调"好代码"的成本 |
| 13 | 囤积知识并重新组合 | Willison | Agentic Engineering 特有的元认知原则 |
| 14 | AI 应该产出更好的代码 | Willison | Vibe Coding 接受更差的代码；Agentic Engineering 要求更好的代码 |
| 15 | 探索性原型 | Willison | Agentic Engineering 特有的技术选型方法 |
| 16 | 不强加未经审查的代码 | Willison | Agentic Engineering 的底线原则 |
| 17 | AGENTS.md 是诊断工具 | Osmani | Vibe Coding 使用自动生成的泛化配置；Agentic Engineering 要求手动编写精准上下文 |
