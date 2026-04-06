# 上下文工程与代理-计算机接口（ACI）

> **核心来源：**
> - Anthropic《Building Effective Agents》附录 2（2024.12）
> - Claude Code 官方文档《Best Practices》CLAUDE.md 章节、《How Claude Code works》上下文管理章节
> - Claude Code 官方文档《Extend Claude Code》Skills/Hooks/MCP 章节

---

## 一、上下文工程（Context Engineering）

### 1.1 定义与定位

上下文工程是一门围绕 LLM 的上下文窗口进行设计和管理的技术学科。如果说 Prompt Engineering 关注的是"如何写好一段提示"，那么 Context Engineering 关注的是**"如何构建代理运行的完整信息环境"**——包括输入什么信息、按什么顺序、在什么时机、以什么格式。

Anthropic 在 Claude Code 文档中虽然没有直接使用"Context Engineering"这个术语，但其整个代理系统设计的核心实际上就是上下文工程。Claude Code 文档开篇最重要的论断是：

> "Most best practices are based on one constraint: **Claude's context window fills up fast, and performance degrades as it fills.**"

这个约束是理解上下文工程的出发点：**上下文窗口是最稀缺的资源，所有设计决策都应该围绕如何高效利用这个资源来展开。**

### 1.2 上下文的组成

根据 Claude Code 的技术文档，代理的上下文窗口包含以下内容：

| 内容 | 说明 | 大小/时机 |
|------|------|-----------|
| **系统指令** | Claude Code 的核心系统提示词 | 固定大小，始终加载 |
| **CLAUDE.md** | 项目级持久化指令 | 手动编写，每次会话加载 |
| **Auto Memory** | Claude 自动保存的项目模式和学习 | 前 200 行或 25KB，每次会话加载 |
| **Skills 内容** | 按需加载的技能知识 | 仅在相关时加载描述，使用时加载全文 |
| **对话历史** | 所有消息、工具调用和结果 | 随会话增长 |
| **文件内容** | 代理读取的文件 | 按需加载 |
| **命令输出** | 代理运行命令的输出 | 按需产生 |
| **MCP 工具定义** | 外部工具的描述 | 按需加载，仅名称消耗上下文 |

### 1.3 上下文的生命周期管理

**加载阶段（Session Start）：**
- 系统指令（固定）
- CLAUDE.md（如果存在）
- Auto Memory 的前 200 行（如果存在）
- Skills 的描述列表（完整内容仅在技能被使用时加载）

**运行阶段（During Session）：**
- 文件读取内容
- 命令执行输出
- 对话历史（包括用户的输入和代理的响应）

**压缩阶段（When Approaching Limit）：**
- 自动压缩（约 95% 容量时触发）
- 手动压缩（`/compact`）
- 回滚并总结（`Esc + Esc` → "Summarize from here"）
- 完全清除（`/clear`）

**持久化阶段（Across Sessions）：**
- CLAUDE.md（手动维护，跨会话持久化）
- Auto Memory（自动积累，跨会话持久化）
- 子代理 Memory（项目级或用户级持久化）

---

## 二、CLAUDE.md：项目级上下文工程

CLAUDE.md 是上下文工程在项目级别最核心的实现工具。Claude Code 在每次会话开始时自动读取此文件。

### 2.1 Claude Code 官方的最佳实践

以下内容完全基于 Claude Code 官方文档的指导。

**应该包含什么：**

| 类别 | 示例 | 理由 |
|------|------|------|
| 代理猜不到的 Bash 命令 | 构建命令、测试命令、部署命令 | 代理无法从代码推断这些 |
| 与默认不同的代码风格 | "使用 ES modules 而非 CommonJS" | 代理默认遵循语言惯例 |
| 测试指令和首选运行器 | "用单个测试运行，不用整个测试套件" | 影响代理如何验证工作 |
| 仓库规范 | 分支命名、PR 约定 | 代理不知道团队惯例 |
| 架构决策 | "支付模块使用事件驱动架构" | 代理需要知道项目特定的决策 |
| 环境特殊配置 | 需要设置的环境变量 | 代理无法推断 |
| 常见陷阱 | "token 需要先 refresh 才能使用" | 非显而易见的行为 |

**不应该包含什么：**

| 类别 | 理由 |
|------|------|
| 代理通过读代码能推断的信息 | 浪费上下文 |
| 标准语言约定 | 代理已经知道 |
| 详细的 API 文档 | 应链接到文档而非内联 |
| 经常变化的信息 | 容易过时 |
| 长篇解释或教程 | 消耗大量上下文 |
| 文件级别的代码库描述 | 代理可以直接读文件 |
| 自明之理的实践 | 如"写干净的代码" |

### 2.2 CLAUDE.md 的层级结构

Claude Code 支持多层 CLAUDE.md，按优先级从高到低：

| 位置 | 作用域 | 是否提交到 Git |
|------|--------|---------------|
| `~/.claude/CLAUDE.md` | 所有项目的个人偏好 | 否 |
| `./CLAUDE.md` | 当前项目（团队共享） | 是 |
| `./CLAUDE.local.md` | 当前项目（个人） | 否（应加入 .gitignore） |
| 父目录 | Monorepo 的上层目录 | 视情况 |
| 子目录 | 子目录特定指令 | 视情况 |

CLAUDE.md 支持通过 `@path/to/file` 语法导入其他文件：

```markdown
See @README.md for project overview and @package.json for available npm commands.

# Additional Instructions
- Git workflow: @docs/git-instructions.md
- Personal overrides: @~/.claude/my-project-instructions.md
```

### 2.3 CLAUDE.md 的维护原则

Claude Code 文档将其类比为代码：

> "Treat CLAUDE.md like code: review it when things go wrong, prune it regularly, and test changes by observing whether Claude's behavior actually shifts."

**关键原则：保持简洁。** 如果 CLAUDE.md 太长，代理会忽略其中的重要规则。Claude Code 文档警告：

> "If Claude keeps doing something you don't want despite having a rule against it, the file is probably too long and the rule is getting lost. If Claude asks you questions that are answered in CLAUDE.md, the phrasing might be ambiguous."

**增强遵守度的技巧：** 可以在规则前添加强调标记，如 "IMPORTANT" 或 "YOU MUST"。

**Addy Osmani 的批评：** Osmani 在 [Stop Using /init for AGENTS.md](https://addyosmani.com/blog/agents-md/) 中指出，自动生成的 CLAUDE.md（通过 `/init` 命令）往往是泛化的模板，不如手工编写有针对性。Claude Code 的文档对此做了折中——`/init` 生成初始版本，然后开发者在此基础上精炼。

---

## 三、代理-计算机接口（ACI）

### 3.1 定义与重要性

代理-计算机接口（Agent-Computer Interface, ACI）是 Anthropic 在《Building Effective Agents》附录 2 中首次正式提出的概念。它关注的是**如何设计工具、文档和接口，使 AI 代理能高效、准确地与计算机系统交互。**

ACI 是 HCI（人机交互）在代理时代的对应物。Anthropic 的原则是：

> "One rule of thumb is to think about how much effort goes into human-computer interfaces (HCI), and plan to invest just as much effort in creating good agent-computer interfaces (ACI)."

### 3.2 Anthropic 的六条 ACI 设计原则

这些原则来自 Anthropic 构建 SWE-bench 代理的实战经验。

**原则一：站在模型的角度思考**

> "Put yourself in the model's shoes. Is it obvious how to use this tool, based on the description and parameters, or would you need to think carefully about it?"

好的工具定义应包含：使用示例、边缘情况说明、输入格式要求、与其他工具的清晰边界。

**原则二：优化参数命名和描述**

> "How can you change parameter names or descriptions to make things more obvious? Think of this as writing a great docstring for a junior developer on your team."

这在代理使用多个相似工具时尤为重要。

**原则三：测试代理如何使用工具**

> "Run many example inputs in our workbench to see what mistakes the model makes, and iterate."

**原则四：防错设计（Poka-yoke）**

> "Change the arguments so that it is harder to make mistakes."

Anthropic 的 SWE-bench 案例：模型在使用相对文件路径时经常犯错（因为代理已经移出了根目录），将工具改为始终要求绝对文件路径后，模型就无缺陷地使用了。

**原则五：给模型足够的"思考"空间**

> "Give the model enough tokens to 'think' before it writes itself into a corner."

**原则六：保持格式自然**

> "Keep the format close to what the model has seen naturally occurring in text on the internet."

### 3.3 工具格式选择的经验法则

| 场景 | 推荐 | 避免 | 理由 |
|------|------|------|------|
| 文件编辑 | 完整文件重写 | Diff 格式 | Diff 需要预知变更行数，LLM 难以准确计算 |
| 代码输出 | Markdown 代码块 | JSON 内嵌代码 | JSON 需要额外的换行和引号转义 |
| 文件路径 | 绝对路径 | 相对路径 | 相对路径在代理移动目录后不可靠 |

### 3.4 ACI 的扩展层次

在 Claude Code 的生态中，ACI 不只是工具设计，而是一个分层的扩展体系：

| 层级 | 机制 | 作用 |
|------|------|------|
| **第一层：内置工具** | Read, Edit, Bash, Grep, Glob 等 | 代理的基本能力 |
| **第二层：CLAUDE.md** | 项目级指令文件 | 约束代理行为 |
| **第三层：Skills** | `.claude/skills/` 下的 SKILL.md 文件 | 按需加载的领域知识和工作流 |
| **第四层：Hooks** | 生命周期钩子脚本 | 确定性执行的操作（如每次编辑后运行 linter） |
| **第五层：MCP** | 外部工具服务器 | 连接外部服务和数据源 |
| **第六层：子代理** | 自定义的专门代理 | 独立上下文中的专门任务 |

---

## 四、Skills（技能）：按需加载的领域知识

### 4.1 概念

Skills 是 Claude Code 中上下文工程的重要机制。它们是 Markdown 文件，包含领域知识和可复用的工作流。关键特性是**按需加载**——Claude 在会话开始时只看到 Skills 的描述列表，完整内容仅在技能被使用时才加载到上下文中。

### 4.2 Skills 的两种使用方式

**方式一：代理自动调用**

当 Claude 认为某个 Skill 与当前任务相关时，它会自动加载和使用该 Skill 的内容。

**方式二：用户手动调用**

用户通过 `/skill-name` 显式调用 Skill。

### 4.3 Skill 的文件格式

```markdown
---
name: api-conventions
description: REST API design conventions for our services
---
# API Conventions
- Use kebab-case for URL paths
- Use camelCase for JSON properties
- Always include pagination for list endpoints
- Version APIs in the URL path (/v1/, /v2/)
```

### 4.4 可执行 Skill（定义工作流）

Skills 也可以定义可重复执行的工作流：

```markdown
---
name: fix-issue
description: Fix a GitHub issue
disable-model-invocation: true
---
Analyze and fix the GitHub issue: $ARGUMENTS.

1. Use `gh issue view` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
```

`disable-model-invocation: true` 表示这个 Skill 有副作用，应该只在用户手动调用时才执行。

---

## 五、Hooks（钩子）：确定性执行的操作

### 5.1 概念

Hooks 是在代理工作流特定节点自动运行的 Shell 脚本。与 CLAUDE.md 中的指令不同，Hooks 是**确定性的**——它们保证在每次触发时都会执行，而不是依赖代理去"遵守"指令。

### 5.2 Hooks 的类型

| 事件 | 触发时机 |
|------|----------|
| `PreToolUse` | 代理使用工具之前 |
| `PostToolUse` | 代理使用工具之后 |
| `SubagentStart` | 子代理开始执行时 |
| `SubagentStop` | 子代理完成时 |
| `Stop` | 代理完成整个任务时 |

### 5.3 实际应用

**自动格式化：** 每次文件编辑后自动运行 linter。

**预提交检查：** 提交前自动运行安全扫描。

**数据库查询保护：** 使用 PreToolUse Hook 验证 Bash 命令是否包含写操作（INSERT/UPDATE/DELETE）。

### 5.4 Hooks 与 CLAUDE.md 的互补关系

| 特性 | CLAUDE.md 指令 | Hooks |
|------|---------------|-------|
| 执行保证 | 建议性的，代理可能忽略 | 确定性的，一定会执行 |
| 适用场景 | 一般行为引导 | 必须每次执行的操作 |
| 上下文消耗 | 每次会话加载 | 仅在触发时运行 |

---

## 六、MCP（模型上下文协议）

### 6.1 概述

MCP 是 Anthropic 于 2024 年 11 月提出的开放标准协议，为 AI 模型提供与外部工具和数据源的标准化连接方式。类比 AI 领域的"USB 接口"。

### 6.2 上下文工程视角下的 MCP

MCP 在上下文工程中有两个重要特性：

**延迟加载（Deferred Loading）：** MCP 工具的定义默认是延迟加载的。Claude Code 在会话开始时只看到工具名称（不消耗大量上下文），当代理实际需要使用某个工具时才加载其完整定义。

**按需连接：** 可以通过子代理的 `mcpServers` 配置为特定子代理提供专属的 MCP 服务器，而不在主对话中加载这些工具的描述。

### 6.3 MCP 的连接类型

| 类型 | 说明 | 适用场景 |
|------|------|----------|
| **Stdio** | 通过标准输入/输出通信 | 本地工具（文件系统、数据库） |
| **SSE** | 通过服务器推送事件通信 | 远程服务 |
| **HTTP** | 通过 HTTP 请求通信 | 远程 API |
| **WS** | 通过 WebSocket 通信 | 实时双向通信 |
