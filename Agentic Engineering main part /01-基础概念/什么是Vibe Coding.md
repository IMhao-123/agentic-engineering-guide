# 什么是 Vibe Coding？

> **核心来源：**
> - Andrej Karpathy 原始推文（2025 年 2 月 6 日， X 平台）
> - Simon Willison《Not all AI-assisted programming is vibe coding (but vibe coding rocks)》（2025 年 3 月 19 日）
> - Addy Osmani《Agentic Engineering》（2026 年 2 月 4 日）

---

## 一、定义

**Vibe Coding**（氛围编码）是一种极度依赖直觉和 AI 生成能力的开发方式。其核心特征是：**开发者不审查代码差异（Diff），不阅读生成的代码，纯粹通过自然语言指令驱动 LLM，盲目接受输出或通过粘贴报错信息来迭代。**

该术语由 **Andrej Karpathy** 于 **2025 年 2 月 6 日** 首次提出。Karpathy 是 OpenAI 的联合创始人之一，曾任 Tesla AI 总监，是 AI 社区中极具影响力的人物。

---

## 二、Karpathy 的原始定义

来源：Simon Willison 引述 Karpathy 的原始推文（2025 年 3 月 19 日）

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and **forget that the code even exists**. It's possible because the Llms (e.g. Cursor Composer w Sonnet) are getting too good. Also I just talk to Composer with SuperWhisper so I barely even touch the keyboard."
>
> "I ask for the dumbest things like 'decrease the padding on the sidebar by half' because I'm too lazy to find it. **I 'Accept All' always, I don't read the diffs anymore.** When I get error messages I just copy paste them in with no comment, usually that fixes it. The code grows beyond my usual comprehension, I'd have to really read through it for a while. Sometimes the LLMs can't fix a bug so I just work around it or ask for random changes until it goes away."
>
> "**It's not too bad for throwaway weekend projects, but still quite amusing.** I'm building a project or webapp, but it's not really coding—I just see stuff, say stuff, run stuff, and copy paste stuff, and it mostly works."
>
> — Andrej Karpathy（2025.02.06）

**关键特征总结：**
- **忘记代码甚至存在**——核心定义
- **总是接受所有（Accept All always）**——不阅读 Diff
- **直接粘贴报错**——不做解释
- **代码超出理解范围**——也不去看
- **仅适用于一次性周末项目**

---

## 三、Willison 的权威阐释

Simon Willison 在 2025 年 3 月 19 日的博客文章中对 Vibe Coding 做出了系统性阐释：

**核心定义：**

> "When I talk about vibe coding I mean **building software with an LLM without reviewing the code it writes**."
>
> — Simon Willison

**Vibe Coding 的适用场景：**

Willison 列出了 Vibe Coding 真正适合的场景：
- 项目应该是**低风险的（low stakes）**
- 考虑安全性（secrets、data privacy）
- 做好的网络公民
- 涉及金钱时需要特别小心

**Willison 的核心观点：**

> "I *love* this definition. Andrej is an extremely talented and experienced programmer—he has no need for AI assistance at all. He's using LLMs like this because it's fun to try out wild new ideas, and the speed at which an LLM can produce code is an order of magnitude faster than even the most skilled human programmers. **For low stakes projects and prototypes why not just let it rip?**"
>
> — Simon Willison

**Vibe Coding 的价值：**

> "I believe **everyone deserves the ability** to automate tedious tasks in their lives with computers. You shouldn't need a computer science degree or programming bootcamp in order to get computers to do extremely specific tasks for you."
>
> "If vibe coding grants millions of new people the ability to build their own custom tools, I could not be happier about it."
>
> — Simon Willison

---

## 四、典型工作模式

| 步骤 | 行为 | 说明 |
|------|------|------|
| 1. 提示 | 用自然语言描述想要的功能 | 可以是最琐碎的要求 |
| 2. 接受 | 盲目接受 LLM 生成的所有代码 | 不阅读 Diff，不检查代码 |
| 3. 运行 | 直接运行 | 不做任何审查 |
| 4. 迭代 | 如果崩溃，把报错信息粘贴回输入框 | 不做解释，直接粘贴 |
| 5. 重复 | 直到"看起来能用"为止 | 以能用为唯一标准 |

---

## 五、适用场景

Vibe Coding 并非毫无价值。Karpathy、Willison 和 Osmani 都明确列出了它适用的场景：

### 5.1 推荐场景

| 场景 | 说明 |
|------|------|
| **快速原型和 MVP** | 需要在周末之前做出一个能跑的东西，代码质量无关紧要 |
| **个人脚本和一次性工具** | 你是唯一的用户，坏了就重新生成 |
| **学习和探索** | 新手可以通过 AI 的输出学习编程，从示例中学习 |
| **创意头脑风暴** | 故意"过度生成"来探索不同的实现方案，然后丢弃并正确构建 |
| **经验开发者的实验** | Karpathy 本人在做"throwaway weekend projects"时使用 |

### 5.2 不适用场景

| 场景 | 原因 |
|------|------|
| 生产级软件 | 安全、可靠性、可维护性都是必须的 |
| 多人使用的工具 | 安全漏洞和 bug 会影响他人 |
| 涉及敏感数据 | 代码未经审查，可能泄露数据 |
| 涉及计费 API | 可能产生意外费用 |
| 团队协作项目 | 代码质量会影响团队 |

---

## 六、致命缺陷

### 6.1 技术债务

> "This isn't engineering, it's hoping."
>
> — Addy Osmani 引述一位工程师的话

Vibe Coding 的经典失败模式总是相同的：**演示时看起来很好，现实来临时就崩溃了。**

当你尝试修改它、扩展它或保护它时，你会发现自己根本不理解代码到底在做什么。

### 6.2 安全隐患

- 引入隐蔽的安全漏洞
- 依赖项可能包含恶意代码
- 无法审计代码中嵌入的敏感信息（API 密钥、凭据等）
- 私人数据可能被意外泄露

### 6.3 不可维护性

- 代码结构混乱，缺乏模块化
- 没有测试用例
- 没有文档
- 任何人（包括原作者）都无法理解代码的真实行为

### 6.4 技能退化风险

Osmani 警告：

> "If you're junior and you lean on AI before building those fundamentals, you risk a dangerous skill atrophy. You can produce code without understanding it. You can ship features without learning why certain patterns exist."
>
> — Addy Osmani

初学者过度依赖 Vibe Coding 可能：
- 能生成代码但不理解代码
- 能提交功能但不理解为什么需要某些设计模式
- 缺乏调试和推理能力

---

## 七、Vibe Coding ≠ AI 辅助编程

**这是一个关键区分。** Simon Willison 用了一整篇文章来澄清这个混淆：

> "I'm concerned that the definition is already escaping its original intent. I'm seeing people apply the term 'vibe coding' to all forms of code written with the assistance of AI. I think that both dilutes the term and gives a false impression of what's possible with responsible AI-assisted programming."
>
> "**Vibe coding is *not* the same thing as writing code with the help of LLMs!**"
>
> — Simon Willison

**区分标准：**

| 维度 | Vibe Coding | AI 辅助编程（负责任的） |
|------|-------------|------------------------|
| 是否审查代码 | 否 | 是 |
| 是否阅读 Diff | 否 | 是 |
| 是否理解代码 | 否 | 是——能向他人解释 |
| 是否有测试 | 否 | 是 |
| 是否适用于生产 | 否 | 是 |

**Willison 的黄金法则：**

> "My golden rule for production-quality AI-assisted programming is that **I won't commit any code to my repository if I couldn't explain exactly what it does to somebody else**."
>
> — Simon Willison

---

## 八、Osmani 对术语泛化的批评

> "The problem is that 'vibe coding' has become a suitcase term. People now use it to describe everything from a weekend hack to a disciplined engineering workflow where AI agents handle implementation under human oversight. **These are fundamentally different activities, and conflating them is causing real confusion—and real damage.**"
>
> — Addy Osmani

当人们无法区分"周末的快速原型"和"有纪律的代理辅助生产开发"时，企业和技术管理者就无法做出正确的技术决策。

---

## 九、Vibe Coding 的正面价值

Willison 和 Karpathy 都强调 Vibe Coding 的正面价值：

**对新手：**
- 削平了编程的初始学习曲线
- 让非程序员也能构建自己的工具
- 是进入编程世界的好入口

**对经验丰富的开发者：**
- 快速实验和原型验证
- 建立对 LLM 能力的直觉
- Willison 发布了 80+ 个 Vibe Coding 实验
