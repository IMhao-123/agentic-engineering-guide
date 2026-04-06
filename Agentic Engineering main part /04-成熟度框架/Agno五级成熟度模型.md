# Agno 代理软件五级成熟度模型

> **核心来源：**
> - Agno 官方博文《The 5 levels of agentic software: A progressive framework for building reliable AI agents》（Cosette Cressler, 2026 年 3 月 24 日，作者署名为 Agno 创始人 Ashpreet Bedi 的框架）
> - Agno 官方文档 docs.agno.com AgentOS 章节
> - Agno 博文《Turning your agents into learning machines》（2026 年 1 月 15 日）
> - arXiv:2604.01480《A Self-Evolving Agentic Framework for Metasurface Inverse Design》技能进化概念
> - Anthropic《Building Effective Agents》

> **方法论说明：** 本框架来源于 Agno Inc.（商业公司）创始人 Ashpreet Bedi 提出的实践经验总结，发布于 Agno 官方博客。它不是学术界公认的通用标准，但提供了从工程实践角度理解代理能力演进的有价值的思维框架。使用时应结合自身项目需求灵活调整，并注意其与 Anthropic 等其他框架的交叉验证。

---

## 一、框架概述：一个反"过早复杂化"的成熟度模型

Agno 的五级成熟度框架（The Five Levels of Agentic Software）描述了代理式软件从最基础到完全自主的演进路径。其核心理念不是"如何构建最强大的代理"，而是一个更具反思性的问题：**你真的需要那么复杂的代理吗？**

### 1.1 框架提出的背景

Agno 的博文开篇描述了一个常见的陷阱：

> "The agent is running. The demo went great. The investor call is booked. Then the real questions start. Why does it forget everything between sessions? Why is it ignoring the style guide your team spent three weeks refining? Why is it repeating research that was already completed, sometimes twice in the same session, driving up token costs that now need to be explained to finance?"

许多团队直接跳到多代理编排，因为这在 Demo 中看起来令人印象深刻。但几周后他们才意识到，一个有良好指令和适当基础设施的单一代理本来可以解决 80% 的问题——没有协调失败、上下文混乱或成本失控。

### 1.2 核心前提

> "Every level adds complexity, and complexity comes with real cost. That cost only makes sense to pay when the simpler approach has clearly hit its ceiling."
>
> — Agno《The 5 levels of agentic software》

每一级都增加了复杂度，而复杂度有真实的成本。只有当更简单的方案已经明显触及天花板时，这些成本才有意义。

### 1.3 框架的作者与可信度

该框架由 Agno 创始人兼 CEO Ashpreet Bedi 开发，Agno 是一个开源代理框架（GitHub 37K+ stars），提供 AgentOS 生产运行时。框架以博文形式发布于 Agno 官方博客（2026 年 3 月 24 日），附带了完整的可运行代码示例（Cookbook: `agno.link/agent-levels`）。

需要注意的是，作为商业公司提出的框架，它自然与 Agno 的产品层级对应（Agent → Agent + Knowledge → Agent + Learning → Team → AgentOS）。这种对应关系既是优势（有具体的技术实现支撑），也是需要注意的偏见来源（可能倾向于推荐更多的框架依赖）。

---

## 二、各级别详解

### Level 1：无状态代理（Stateless Agents）

**定义：** 一个 LLM 加上工具调用能力，每次会话从零开始。

**核心能力：**
- LLM 结合外部工具调用（Function Calling）
- 对于编码代理，最小可行工具集是：读取文件、写入文件、运行 Shell 命令
- 单轮或多轮对话，但无跨会话持久化

**技术特征：**
- 所有上下文必须在当前会话中提供
- 无记忆、无知识库、无学习机制
- "It works. It can solve real problems."（Agno 原文）

**代码示例（Agno）：**
```python
from agno.agent import Agent
from agno.models.openai import OpenAIResponses
from agno.tools.coding import CodingTools

agent = Agent(
    name="Gcode",
    model=OpenAIResponses(id="gpt-5.2"),
    instructions=(
        "You are a coding agent. Write clean, well-documented code. "
        "Always save your work to files and test by running them."
    ),
    tools=[CodingTools(base_dir=WORKSPACE, all=True)],
    markdown=True,
)
```

**适用场景：**
- 孤立的、自包含的任务
- 原型验证
- 一次性任务处理

**不适用场景：**
- 需要遵循团队规范的任务（没有知识库）
- 需要跨会话连续性的任务（没有记忆）
- 需要从经验中改进的任务（没有学习机制）

---

### Level 2：存储与知识库（Storage & Knowledge）

**定义：** 代理拥有会话存储和领域知识，可以在会话间保持连续性。

**Level 1 → Level 2 增加的能力：**

**会话存储（Session Storage）：**
- 每个会话保存到数据库
- 代理可以访问最近的聊天历史（如最近 3 次交互）并包含在上下文窗口中
- 创建可靠的会话记录，可用于追踪代理的行动、决策和输出
- 技术实现：SQLite（开发）/ PostgreSQL（生产）

**领域知识库（Domain Knowledge）：**
- 结构化、可搜索的信息存储
- 包含：架构文档、设计决策、内部操作手册、团队讨论
- 使用语义搜索 + 关键词搜索（Hybrid Search）在运行时检索相关信息
- 技术实现：ChromaDb（开发）/ PgVector（生产）+ 嵌入模型

**代码示例（Agno）：**
```python
from agno.db.sqlite import SqliteDb
from agno.knowledge import Knowledge
from agno.vectordb.chroma import ChromaDb, SearchType

db = SqliteDb(db_file="agents.db")
knowledge = Knowledge(
    vector_db=ChromaDb(
        collection="coding-standards",
        search_type=SearchType.hybrid,
        embedder=OpenAIEmbedder(id="text-embedding-3-small"),
    ),
)

agent = Agent(
    name="Gcode",
    tools=[CodingTools(base_dir=WORKSPACE, all=True)],
    knowledge=knowledge,
    search_knowledge=True,
    db=db,
    add_history_to_context=True,
    num_history_runs=3,
)
```

**适用场景：**
- 需要遵循团队编码规范的内部工具
- 期望对话能从中断处继续的场景
- Agno 认为"对于大多数内部工具，这是最佳平衡点"

**不适用场景：**
- 需要从交互中提取经验并改进的场景（Level 3）
- 需要多代理协作的场景（Level 4）

---

### Level 3：学习型代理（Learning Machines）

**定义：** 代理具备从经验中学习和改进的能力，知识可以跨会话/用户积累。

> "The jump from Level 2 to Level 3 is the most significant one in the framework. At Level 2, the agent follows rules that are given to it. At Level 3, it learns rules from experience."
>
> — Agno《The 5 levels of agentic software》

**Level 2 → Level 3 增加的能力：**

**代理记忆（Agentic Memory）：**
- 从对话中提取事实
- 识别跨会话的模式
- 将学习成果综合为持久化的记忆存储
- 影响未来的行为

**持续学习（Continuous Learning）：**
- Agno 称之为"GPU Poor Continuous Learning"——不微调、不重训练的持续改进
- "The model doesn't get smarter. The system does."
- 随着基础模型的改进，系统自动受益

**推理工具（Reasoning Tools）：**
- 代理在行动前先反思（reflect before acting）

**代码示例（Agno）：**
```python
from agno.learn import LearnedKnowledgeConfig, LearningMachine, LearningMode

learned_knowledge = Knowledge(
    vector_db=ChromaDb(collection="coding-learnings"),
)

agent = Agent(
    name="Gcode",
    tools=[CodingTools(all=True), ReasoningTools()],
    knowledge=docs_knowledge,
    search_knowledge=True,
    learning=LearningMachine(
        knowledge=learned_knowledge,
        learned_knowledge=LearnedKnowledgeConfig(
            mode=LearningMode.AGENTIC,
        ),
    ),
    enable_agentic_memory=True,
    db=db,
)
```

**两轮对话测试：**
- Session 1：用户告诉代理偏好函数式编程风格
- Session 2：一个完全不同的任务——代理搜索其学习成果，找到该偏好，自动使用函数式代码

**学术支撑：** arXiv:2604.01480（2026 年 3 月）在超表面逆向设计的场景中验证了"技能进化"（Skill Evolution）概念——代理通过编码代理 + 演进技能制品 + 确定性评估器的耦合，在不修改模型权重的情况下实现跨任务的可复用工作流知识积累。进化后的技能将分布内任务成功率从 38% 提升到 74%，标准通过率从 0.510 提升到 0.870。

**适用场景：**
- 为同一用户反复服务的个人编码助手
- 团队工具需要共享学习成果
- "按我们喜欢的方式做"有意义的任何上下文

---

### Level 4：多代理协作（Multi-Agent Teams）

**定义：** 由负责不同工作的专业代理组成的团队，由团队领导者协调。

> "Most teams skip straight to this level. That's the mistake the framework is specifically designed to prevent."
>
> — Agno《The 5 levels of agentic software》

**Level 3 → Level 4 增加的能力：**
- 一个代理变为多个，每个有定义的角色、定制的工具集和清晰的关注点分离
- Team 对象协调它们，路由任务和聚合响应
- `show_members_responses=True` 让每个代理的输出可见

**代码示例（Agno）：**
```python
from agno.team.team import Team

coder = Agent(
    name="Coder",
    role="Write code based on requirements",
    tools=[CodingTools(all=True)],
)

reviewer = Agent(
    name="Reviewer",
    role="Review code for quality, bugs, and best practices",
    tools=[CodingTools(enable_write_file=False,
                       enable_edit_file=False,
                       enable_run_shell=False)],
)

tester = Agent(
    name="Tester",
    role="Write and run tests for the code",
    tools=[CodingTools(all=True)],
)

coding_team = Team(
    name="Coding Team",
    members=[coder, reviewer, tester],
    show_members_responses=True,
)
```

**Agno 的重要警告：**
> "Multi-agent teams are powerful but unpredictable. The team leader is an LLM making delegation decisions — sometimes it delegates well, sometimes it doesn't. For production systems where reliability matters, prefer explicit workflows over dynamic teams."

**适用场景：**
- 需要多视角的任务（代码审查是完美示例）
- 任务自然分解为专家角色
- 有人在审查输出后才会发布的人机协作环境

**不适用场景：**
- 可靠性至关重要的生产系统（除非使用显式工作流而非动态团队）
- 单一代理加良好基础设施就能解决的问题

---

### Level 5：生产运行时（Production Runtime）

**定义：** 将 Level 1-4 转化为生产服务的运行时系统。

> "Level 5 is distributed systems engineering with probabilistic reasoning in the execution path."
>
> — Agno《The 5 levels of agentic software》

**Level 4 → Level 5 增加的能力：**
- 开发数据库替换为生产数据库（PostgreSQL + PgVector）
- 真实的连接池、真实的备份、真实的并发访问
- AgentOS 将代理包装为 FastAPI 应用，内置 Web UI、会话管理和追踪
- 追踪（`tracing=True`）提供每个工具调用、每个知识搜索、每个委托决策的可观测性

**代码示例（Agno）：**
```python
from agno.os import AgentOS

agent_os = AgentOS(
    id="Gcode OS",
    agents=[coding_agent],
    teams=[coding_team],
    config=config_path,
    tracing=True,
)
app = agent_os.get_app()

# FastAPI 应用，可直接部署
agent_os.serve(app="run:app", reload=True)
```

**适用场景：**
- 代理不再只是你的——多用户、有可用性要求
- 需要调试生产问题
- 需要完整的监控、追踪和评估系统

---

## 三、与 Anthropic 建议的交叉验证

Agno 的五级框架和 Anthropic《Building Effective Agents》的建议从不同角度描述了同一组原则。

### 3.1 复杂度态度

| 维度 | Agno | Anthropic |
|------|------|-----------|
| **起点** | Level 1（无状态代理 = LLM + 工具） | 简单提示（Simple Prompts） |
| **演进策略** | 逐级递增，不要越级 | "仅在更简单方案不足时增加复杂度" |
| **核心建议** | "Always start at Level 1" | "Start with simple prompts" |
| **对多代理的态度** | "Most teams skip straight to this level. That's the mistake the framework is specifically designed to prevent." | "Multi-agent systems add significant complexity" |

两者的核心理念完全一致：**从最简单的方案开始，只在需要时增加复杂度。**

### 3.2 Agno 各级别与 Anthropic 工作流模式的对应

| Agno 级别 | 对应的 Anthropic 工作流 | 说明 |
|-----------|------------------------|------|
| Level 1 | Enhanced LLM + Tool Use | 基础的 LLM + 工具调用 |
| Level 2 | 同上 + RAG（知识检索） | 增加了外部知识检索 |
| Level 3 | Evaluator-Optimizer（自我改进循环） | 代理从经验中学习和改进 |
| Level 4 | Orchestrator-Workers / Parallelization | 多代理协作 |
| Level 5 | 同上 + 生产基础设施 | 完整的生产运行时 |

### 3.3 关键差异

Agno 框架强调了 Anthropic 没有明确涉及的两个维度：

1. **学习/改进（Level 3）：** Anthropic 的工作流模式主要关注任务执行的结构，而 Agno 的 Level 3 专门关注代理如何从经验中改进——这是一个更长期的维度。
2. **生产运维（Level 5）：** Anthropic 的文章更多是关于"如何构建有效的代理"，而 Agno 的 Level 5 专门关注"如何将代理部署为生产服务"。

---

## 四、Claude Code 在各级别的体现

Claude Code 作为一个完整的编码代理工具，实际上覆盖了所有五个级别的功能，但其安全模型暗示了一个递进式的采用路径：

| Agno 级别 | Claude Code 对应功能 | 说明 |
|-----------|---------------------|------|
| Level 1 | 基础工具（Read/Edit/Bash/WebFetch） | 核心代理能力 |
| Level 2 | CLAUDE.md + Skills + Auto Memory | CLAUDE.md = 知识库；Auto Memory = 会话存储 |
| Level 3 | Auto Memory（跨会话学习） | Auto Memory 可以在会话间积累学习成果 |
| Level 4 | Subagents + Agent Teams | 子代理和代理团队 |
| Level 5 | Cloud + OpenTelemetry + Managed Settings | 云端执行、可观测性、组织管理 |

---

## 五、实践建议

### 5.1 如何评估你所在的级别

问自己这些问题：

1. **你的代理有跨会话的记忆吗？**
   - 没有 → Level 1
   - 有 → 继续下一个问题

2. **你的代理能从历史交互中学习吗？**
   - 不能 → Level 2
   - 能 → 继续下一个问题

3. **你需要多个代理协作吗？**
   - 不需要 → Level 3
   - 需要 → 继续下一个问题

4. **你需要生产级部署和运维吗？**
   - 不需要 → Level 4
   - 需要 → Level 5

### 5.2 "不要越级跳跃"原则

Agno 最核心的建议是：**每一级的基础设施都是下一级的前提。** 如果你的项目还没有可靠的测试套件和基本的代理循环，不要直接跳到多代理系统。

一个实用的验证方法：先在 Level 1 完成任务，然后评估哪些不足是 Level 2 能解决的。如果 Level 2 还不够，再考虑 Level 3——以此类推。

### 5.3 Level 2 是大多数团队的最佳平衡点

Agno 明确表示："Level 2 is the sweet spot for most internal tools." 会话存储和知识库解决了最常见的生产问题——上下文丢失和规范盲区——而不需要自主学习或多代理协调的开销。
