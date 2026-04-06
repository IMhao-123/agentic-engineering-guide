# 什么是 Agentic Engineering？

> **核心来源：**
> - Addy Osmani《Agentic Engineering》（2026 年 2 月 4 日）
> - Simon Willison《Writing about Agentic Engineering Patterns》（2026 年 2 月 23 日）
> - Andrej Karpathy 术语建议（2026 年 2 月，X 平台）

---

## 一、定义

**Agentic Engineering**（代理式工程）是一种专业的软件开发方式。在此方式中，开发者以工程纪律为核心，利用 AI 编码代理（Coding Agents）来加速和自动化实现工作，同时保持对架构、质量和正确性的全面掌控。

该术语由 **Andrej Karpathy** 于 **2026 年 2 月** 建议使用，并由 **Addy Osmani** 在其 2026 年 2 月 4 日的博客文章《Agentic Engineering》中系统性地定义和推广。

---

## 二、术语的确立过程

### 2.1 Karpathy 的建议

2026 年 2 月，Andrej Karpathy 在 X 平台上建议使用 "Agentic Engineering" 作为正式术语（推文 ID: 2019137879310836075）。由于 X 平台限制，该推文无法直接抓取，但被 Osmani 和 Willison 引用。

### 2.2 Osmani 的系统性定义

2026 年 2 月 4 日，Addy Osmani 发表长文《Agentic Engineering》，系统性地：
1. 批评了 "Vibe Coding" 被过度泛化使用的问题
2. 回顾了 Willison 的 "Vibe Engineering" 提议
3. 引用了 Karpathy 建议使用 "Agentic Engineering" 的推文
4. 系统定义了 Agentic Engineering 的工作方式及其与 Vibe Coding 的区别

### 2.3 Willison 的采纳

2026 年 2 月 23 日，Simon Willison 正式采用该术语：

> "It looks like the term 'Agentic Engineering' is coming out on top for this now."
>
> — Simon Willison

他开始维护《Agentic Engineering Patterns》——一个持续更新的工程模式指南。

---

## 三、与 Vibe Coding 的本质区别

Osmani 用一句话精准概括：

> "**Vibe coding = YOLO. Agentic engineering = AI does the implementation, human owns the architecture, quality, and correctness.**"
>
> — Addy Osmani

| 对比维度 | Vibe Coding | Agentic Engineering |
|----------|-------------|---------------------|
| **核心态度** | 盲目接受所有输出 | 严格审查每一行代码 |
| **开发者角色** | 提示词 DJ（Prompt DJ） | 系统架构师与质量监督者 |
| **规划** | 无规划，直接开始 | 先写设计文档/规格说明 |
| **代码审查** | 不审查 | 以审查人类同事 PR 的同等级别严格审查 |
| **测试** | 无或极少 | 持续、自动化、全面 |
| **版本控制** | 无规范使用 | 规范的 Git 工作流 |
| **文档** | 无 | 持续维护 |
| **产出物** | 一次性原型、快速 MVP | 长期可维护的生产级软件 |
| **开发速度** | 初期极快 | 初期较慢，后期复利加速 |
| **适用人员** | 所有人（包括非程序员） | 拥有工程基础的专业开发者 |

---

## 四、实际工作方式

### 4.1 先规划，再执行

> "Before prompting anything, you write a design doc or spec—sometimes with AI assistance. You break the work into well-defined tasks. You decide on the architecture. **This is the part vibe coders skip, and it's exactly where projects go off the rails.**"
>
> — Addy Osmani

在向 AI 提示任何代码之前，Agentic Engineering 的实践者会：
- 编写设计文档或规格说明（可以使用 AI 辅助）
- 将工作分解为明确定义的任务
- 确定架构方案
- 设定成功标准

### 4.2 指挥，然后审查

> "You give the AI agent a well-scoped task from your plan. It generates code. **You review that code with the same rigor you'd apply to a human teammate's PR.** If you can't explain what a module does, it doesn't go in."
>
> — Addy Osmani

向 AI 代理分配一个范围明确的任务后：
- 代理生成代码
- 开发者以审查人类同事 Pull Request 的严格程度审查代码
- **如果无法解释一个模块的功能，则不允许合入**

### 4.3 持续测试

> "The single biggest differentiator between agentic engineering and vibe coding is testing. With a solid test suite, an AI agent can iterate in a loop until tests pass, giving you high confidence in the result. **Without tests, it'll cheerfully declare 'done' on broken code.**"
>
> — Addy Osmani

测试是 Agentic Engineering 的**核心使能条件**：
- 拥有可靠的测试套件，AI 代理可以在循环中迭代直到测试通过
- 没有测试，代理会"愉快地宣称完成"但实际上代码是坏的
- 测试是将不可靠的代理转化为可靠系统的关键

### 4.4 拥有代码库

- 维护文档
- 使用版本控制和 CI
- 监控生产环境
- **AI 加速了工作，但开发者对系统负责**

---

## 五、为什么这个术语有效

Osmani 解释了为什么 "Agentic Engineering" 比其他候选名称更合适：

### 5.1 准确描述实际行为

> "You're orchestrating AI agents—coding assistants that can execute, test, and optimize code—while you act as the architect, reviewer, and decision-maker."
>
> — Addy Osmani

你在编排（orchestrating）AI 代理，而你充当架构师、审查者和决策者。

### 5.2 职业可读性

> "'Agentic Engineering' sounds like what it is—a serious engineering discipline involving autonomous agents. You can say it to a VP of Engineering without embarrassment."
>
> — Addy Osmani

你可以在技术会议、招聘、管理层报告中使用这个术语。

### 5.3 清晰划界

术语本身就强制了区分——**Vibe Coding = YOLO，Agentic Engineering = 有纪律的代理辅助开发。**

---

## 六、核心洞察

### 6.1 AI 放大的是现有能力

来源：Simon Willison《Vibe Engineering》（2025.10.07）

> "**AI tools amplify existing expertise.** The more skills and experience you have as a software engineer the faster and better the results you can get from working with LLMs and coding agents."
>
> — Simon Willison

这意味着 Agentic Engineering **不成比例地有利于资深工程师**。如果你有深厚的系统设计、安全模式、性能权衡的基础知识，AI 是一个巨大的力量倍增器。

### 6.2 AI 实际上奖励良好的工程实践

Osmani 指出了一个看似反直觉但极具洞察力的观点：

> "**The irony is that AI-assisted development actually rewards good engineering practices more than traditional coding does.** The better your specs, the better the AI's output. The more comprehensive your tests, the more confidently you can delegate. The cleaner your architecture, the less the AI hallucinates weird abstractions."
>
> — Addy Osmani

传统开发中，差的架构和缺失的测试只是让你自己更痛苦；在 AI 辅助开发中，它们会让代理产生更差的输出。

**AI 不是绕过工程实践的工具，而是放大工程实践效果的工具。** 好的实践被放大成更好的结果，差的实践被放大成更差的结果。

### 6.3 技能需求在上升而非下降

> "Agentic engineering isn't easier than traditional engineering—it's a different kind of hard. **You're trading typing time for review time, implementation effort for orchestration skill, writing code for reading and evaluating code.** The fundamentals matter more, not less."
>
> — Addy Osmani

Agentic Engineering 不是更容易的工程——它是一种不同类型的困难：
- 用编码时间换取审查时间
- 用实现精力换取编排技能
- 用写代码换取读代码和评估代码

### 6.4 Willison 的补充

来源：Simon Willison《Writing about Agentic Engineering Patterns》（2026.02.23）

> "I think of **vibe coding** using its original definition of coding where you pay no attention to the code at all, which today is often associated with non-programmers using LLMs to write code."
>
> "**Agentic Engineering represents the other end of the scale: professional software engineers using coding agents to improve and accelerate their work by amplifying their existing expertise.**"
>
> — Simon Willison

Willison 明确区分了两个端点：
- **Vibe Coding**：不关注代码，非程序员使用 LLM 写代码
- **Agentic Engineering**：专业软件工程师使用编码代理，放大现有专业能力

---

## 七、Willison 的写作原则

来源：Simon Willison《Writing about Agentic Engineering Patterns》

> "I have a strong personal policy of not publishing AI-generated writing under my own name. That policy will hold true for Agentic Engineering Patterns as well. **I'll be using LLMs for proofreading and fleshing out example code and all manner of other side-tasks, but the words you read here will be my own.**"
>
> — Simon Willison

这一原则本身就体现了 Agentic Engineering 的精神：**使用 AI 辅助，但对最终产出负责。**
