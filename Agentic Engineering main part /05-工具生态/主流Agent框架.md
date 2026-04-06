# 主流 Agent 框架

> **核心来源：**
> - Anthropic《Building Effective Agents》对框架的警告
> - OpenAI Agents SDK 官方 GitHub 仓库（openai/openai-agents-python）
> - Agno 官方文档 docs.agno.com 及 AgentOS 博文
> - LangChain / LangGraph 官方文档

> **前置警告：** 在选择框架之前，请先阅读本文第一节"你真的需要框架吗？"。Anthropic 明确建议从直接使用 LLM API 开始，许多代理模式可以用几行代码实现。

---

## 一、你真的需要框架吗？

### 1.1 Anthropic 的警告

Anthropic 在《Building Effective Agents》中用了大量篇幅警告过早使用框架的风险：

> "There are many frameworks that make agentic systems easier to implement. These frameworks make it easy to get started by simplifying standard low-level tasks like calling LLMs, defining and parsing tools, and chaining calls together. However, they often create extra layers of abstraction that can obscure the underlying prompts and responses, making them harder to debug. They can also make it tempting to add complexity when a simpler setup would suffice."
>
> "We suggest that developers start by using LLM APIs directly: many patterns can be implemented in a few lines of code."

这段话包含三个关键观点：

1. **框架简化了入门，但增加了调试难度**——额外的抽象层遮蔽了底层的 prompt 和 response
2. **框架诱使你增加不必要的复杂度**——当简单方案就足够时，框架让复杂方案看起来很诱人
3. **直接使用 API 往往就够了**——许多代理模式只需要几行代码

### 1.2 不需要框架的场景

以下场景直接使用 LLM API 即可：

| 场景 | 直接 API 实现 | 需要框架？ |
|------|-------------|-----------|
| 简单的工具调用 | 几行 Function Calling 代码 | 否 |
| Prompt Chaining | 顺序调用多次 API | 否 |
| 带上下文的单代理 | System prompt + 工具定义 | 否 |
| 简单的 RAG | Embedding + 向量搜索 + LLM | 否 |
| 需要精确控制执行流程 | 自己写循环和条件逻辑 | 否 |

### 1.3 框架可能有价值的场景

| 场景 | 为什么需要框架 |
|------|---------------|
| 多代理协作（3+个代理） | 需要代理间通信、任务路由、结果聚合 |
| 生产级部署 | 需要运行时、监控、认证、可扩展性 |
| 需要内置的记忆和知识管理 | 需要跨会话存储、向量数据库集成 |
| 需要丰富的工具集成 | MCP、内置工具库、自定义工具 |
| 团队协作开发代理系统 | 统一的抽象层、共享配置 |

### 1.4 从"无框架"到"全平台"的光谱

```
直接 API 调用 → 轻量级 SDK → 代理框架 → 全栈平台
(Anthropic推荐)  (OpenAI SDK)  (LangGraph)  (Agno AgentOS)
```

**核心原则：从左向右移动，仅在当前方案明确不够用时才前进。**

---

## 二、OpenAI Agents SDK

### 2.1 概述

OpenAI Agents SDK 是一个**轻量级**的多代理工作流框架。它**不绑定 OpenAI 的模型**——支持 OpenAI Responses API、Chat Completions API 以及 100+ 其他 LLM。

### 2.2 核心概念

| 概念 | 说明 |
|------|------|
| **Agents** | 配置了指令、工具、护栏和 Handoffs 的 LLM |
| **Agents as Tools** | 将一个代理作为另一个代理的工具调用——实现任意深度的代理嵌套 |
| **Handoffs** | 代理间的任务移交机制 |
| **Tools** | 函数、MCP 服务器、托管工具 |
| **Guardrails** | 可配置的输入/输出验证护栏 |
| **Human in the Loop** | 内置的人类干预机制 |
| **Sessions** | 自动化的会话历史管理 |
| **Tracing** | 内置的代理运行追踪，提供可视化 UI |
| **Realtime Agents** | 语音代理支持（gpt-realtime-1.5） |

### 2.3 代码示例

```python
from agents import Agent, Runner

agent = Agent(name="Assistant", instructions="You are a helpful assistant")

result = Runner.run_sync(agent, "Write a haiku about recursion in programming.")
print(result.final_output)
```

### 2.4 特点

- **轻量级：** 最少的抽象层，不遮蔽底层行为
- **模型无关：** 支持 OpenAI、Anthropic、Google、开源模型等 100+ LLM
- **内置追踪：** 提供可视化的追踪 UI
- **语音支持：** 可构建语音代理
- **Handoffs：** 代理间任务移交的标准化机制
- **会话管理：** 自动化的对话历史管理

---

## 三、Agno

### 3.1 概述

Agno 是一个全栈代理平台，包含两个核心产品：
- **Agno Framework：** 开源 Python 代理框架（GitHub 37K+ stars）
- **AgentOS：** 高性能多代理系统的生产运行时

### 3.2 核心能力

| 能力 | 说明 |
|------|------|
| **Memory（记忆）** | 跨会话的持久化记忆，支持 agentic memory |
| **Knowledge（知识库）** | 集成向量数据库（ChromaDb/PgVector）的知识检索，Hybrid Search |
| **Self Learning（自学习）** | LearningMachine 从交互中持续改进，"GPU Poor Continuous Learning" |
| **Guardrails（护栏）** | 输入/输出验证和安全约束 |
| **HITL（人类在环）** | 人工干预机制 |
| **Team（团队）** | 多代理协作，Team Leader 协调 |
| **Workflow（工作流）** | 预定义的代理工作流，顺序执行 |
| **MCP** | 原生 MCP 支持 |

### 3.3 代码示例

```python
from agno.agent import Agent
from agno.models.openai import OpenAIResponses
from agno.tools.coding import CodingTools

agent = Agent(
    name="Gcode",
    model=OpenAIResponses(id="gpt-5.2"),
    instructions="You are a coding agent. Write clean, well-documented code.",
    tools=[CodingTools(base_dir=WORKSPACE, all=True)],
    markdown=True,
)
agent.print_response("Write a Fibonacci function", stream=True)
```

### 3.4 AgentOS 生产运行时

AgentOS 将代理包装为 FastAPI 应用，提供：

| 能力 | 说明 |
|------|------|
| **Web UI** | 内置的交互界面 |
| **会话管理** | 持久化的会话历史 |
| **追踪** | 每个工具调用、知识搜索、委托决策的可观测性 |
| **认证** | JWT 认证、RBAC 权限控制 |
| **隐私** | "你的云"架构，数据留在用户环境中 |
| **部署** | Docker、Railway、AWS ECS 模板 |

### 3.5 性能声明（需谨慎对待）

Agno 官方声称的性能对比数据，未经独立验证：

| 指标 | Agno vs LangGraph | Agno vs CrewAI |
|------|-------------------|----------------|
| 代理实例化速度 | 声称 529x 更快 | 声称 70x 更快 |
| 内存占用 | 声称 24x 更低 | 声称 10x 更低 |

### 3.6 五级成熟度模型

Agno 提出的代理软件五级成熟度框架（详见 04-成熟度框架/Agno五级成熟度模型.md），从 Level 1（无状态代理）到 Level 5（生产运行时），与 Agno 的产品层级直接对应。

---

## 四、LangGraph

### 4.1 概述

LangGraph 是 LangChain 团队开发的代理编排框架，使用**图（Graph）状态机**作为核心抽象。

### 4.2 核心概念

| 概念 | 说明 |
|------|------|
| **Graph（图）** | 定义代理的执行流程为有向图 |
| **Node（节点）** | 图中的执行步骤（LLM 调用、工具执行等） |
| **Edge（边）** | 节点之间的转移 |
| **State（状态）** | 在图中传递的共享状态 |

### 4.3 特点

- **声明式流程定义：** 通过图结构定义代理行为
- **复杂流程支持：** 条件分支、循环、并行等
- **LangChain 生态集成：** 与 LangChain 的工具和模型无缝集成
- **LangGraph Cloud：** 生产级托管运行时

### 4.4 适用场景

需要精确控制执行流程的代理——特别是包含条件分支、循环和并行步骤的复杂工作流。

---

## 五、其他框架

| 框架 | 开发方 | 语言 | 定位 |
|------|--------|------|------|
| **CrewAI** | CrewAI Inc. | Python | 角色扮演式多代理框架——每个代理有角色和背景故事 |
| **Microsoft AutoGen** | Microsoft | Python | 可对话的多代理框架 |
| **Pydantic AI** | Pydantic | Python | 类型安全的代理框架，利用 Pydantic 的类型验证 |
| **Smolagents** | Hugging Face | Python | 轻量级代码代理框架 |
| **Mastra** | Mastra Inc. | TypeScript | TypeScript 代理框架 |

---

## 六、框架选型决策框架

### 6.1 决策树

```
你需要构建代理系统吗？
├── 否 → 直接使用 Claude Code / Codex CLI 等编码代理工具
└── 是 → 你需要多代理协作吗？
    ├── 否 → 你需要精确控制执行流程吗？
    │   ├── 否 → 直接使用 LLM API（Anthropic 推荐）
    │   └── 是 → LangGraph
    └── 是 → 你需要生产级部署和运维吗？
        ├── 否 → OpenAI Agents SDK（轻量）
        └── 是 → Agno AgentOS（全栈）
```

### 6.2 选型对照表

| 需求 | 直接 API | OpenAI SDK | LangGraph | Agno |
|------|---------|-----------|-----------|------|
| 快速原型 | 最快 | 快 | 中等 | 中等 |
| 调试透明度 | 最高 | 高 | 中等 | 中等 |
| 多代理协作 | 手动实现 | Handoffs | 图编排 | Team |
| 生产运行时 | 需自建 | 需自建 | LangGraph Cloud | AgentOS 内置 |
| 记忆/知识库 | 需自建 | Sessions | 需自建 | 内置 |
| 学习曲线 | 低 | 低 | 中等 | 中等 |
| 模型锁定 | 取决于 API | 否（100+ LLM） | LangChain 生态 | 多模型支持 |
| 代码复杂度 | 最低 | 低 | 中等 | 中等 |

### 6.3 Anthropic 的最终建议

> "We suggest that developers start by using LLM APIs directly."

无论选择哪个框架，都应该先尝试直接用 API 实现。如果你发现自己需要反复编写样板代码（工具定义、prompt 管理、会话历史等），再引入框架。框架的价值在于减少重复工作，而不是提供魔法。

---

## 七、编码代理工具 vs Agent 框架

这是一个容易混淆的区别：

| 维度 | 编码代理工具 | Agent 框架 |
|------|------------|-----------|
| **用途** | 直接用来写代码 | 用来构建自定义代理系统 |
| **使用者** | 软件开发者 | 代理系统开发者 |
| **定制程度** | 通过配置文件（CLAUDE.md 等） | 通过代码 |
| **输出** | 代码 | 一个可部署的代理应用 |
| **代表** | Claude Code、Cursor | OpenAI Agents SDK、Agno |

如果你是一名软件工程师想要用 AI 辅助编程，你需要的是**编码代理工具**，不是 Agent 框架。如果你想要构建一个面向终端用户的代理应用（客服机器人、数据分析助手等），你需要的是 **Agent 框架**。
