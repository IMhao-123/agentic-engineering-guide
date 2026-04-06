# agentic-engineering-guide
This is an Agentic Engineering interpret system, Introducing the difference between Vibe Coding and Agentic Engineering—and what Agentic Engineering stands for. We really welcome you to help us improve this guid. 这是一个讲解智能体工程的指南，旨在讲解“氛围编程”与“智能体工程”的区别与意旨。我们非常欢迎您协助完善这份文件。

---
# Agentic Engineering 研究系统

> **研究日期：** 2026年4月5日
>
> **研究性质：** 调查与研究报告
>
> **研究主旨：** 深入剖析 Agentic Engineering（代理式工程）的核心机制、实施路径、工具生态及其与 Vibe Coding（氛围编码）的本质区别。

---

## 研究背景

本系统基于一份初步研究文档的扩展与深化。原文档对 Agentic Engineering 进行了初步介绍，但存在若干事实性错误（如将 "Agentic" 误写为 "Agetic"/"Angelic"，包含未经证实的 GLM-5 学术声明等）。本系统旨在以一手资料为基础，构建一份准确、全面、深入的研究报告。

## 研究方法

本系统严格遵循**一手资料原则**：所有核心论述均优先引用一手来源（官方文档、学术论文、开发者一手博客），仅在必要情况下引用二手及以上的资料。

## 文档结构

```
Agentic Engineering 研究系统/
├── README.md                          ← 你在这里
├── 00-研究概览/
│   ├── 研究背景与方法论.md              ← 研究方法、术语纠正说明
│   └── 术语表与关键人物.md              ← 核心术语定义与领域关键人物
├── 01-基础概念/
│   ├── 什么是Vibe Coding.md            ← Vibe Coding 的定义、特征与局限
│   ├── 什么是Agentic Engineering.md    ← Agentic Engineering 的定义与核心理念
│   └── 范式演进：从Vibe Coding到Agentic Engineering.md  ← 术语演变历史
├── 02-核心理论/
│   ├── 代理架构模式.md                  ← Anthropic 五种核心模式
│   ├── 代理循环设计.md                  ← Agentic Loop 设计原理
│   ├── 上下文工程与ACI.md              ← Context Engineering 与代理-计算机接口
│   └── 多代理协作模式.md                ← 多代理系统设计
├── 03-技术架构/
│   ├── 基础设施与执行环境.md            ← 沙箱、容器化、运行时
│   ├── 验证循环与测试策略.md            ← 测试驱动、自动验证
│   ├── 持久化与容错机制.md              ← 状态恢复、检查点
│   ├── 安全与权限隔离.md                ← 安全威胁模型与防护
│   └── 监控与可观测性.md                ← 追踪、日志、评估
├── 04-成熟度框架/
│   └── Agno代理软件五级成熟度模型.md     ← 从无状态到生产运行的演进路径
├── 05-工具生态/
│   ├── 主流编码代理工具.md              ← Claude Code、Codex CLI、Gemini CLI 等
│   ├── 主流Agent框架.md                ← OpenAI Agents SDK、LangGraph、Agno 等
│   └── 开发环境与工具链.md              ← MCP、AGENTS.md、IDE 集成
├── 06-实战指南/
│   ├── Agentic Engineering核心原则.md   ← 从一手资料提炼的核心原则
│   ├── 代理循环设计实战.md              ← 具体循环设计方法
│   ├── 测试策略与质量保证.md            ← TDD、自动测试、QA
│   └── 常见陷阱与反模式.md              ← 应避免的做法
├── 07-前沿情报/
│   ├── 学术论文追踪.md                  ← arXiv 等平台的关键论文
│   ├── 行业动态与趋势.md                ← 框架更新、产品发布
│   └── 信息获取渠道指南.md              ← 一手信息源推荐
└── 08-参考资料/
    └── 引用与参考源.md                  ← 所有引用的完整列表
```

## 核心发现速览

1. **"Agentic Engineering" 是正确的术语**，源自 Agent（代理），而非 "Angelic"（天使的）。"Angelic Software" 并非社区认可的概念，是原始文档中的一个混淆。
2. **术语起源清晰可考**：Karpathy 于 2025 年 2 月提出 "Vibe Coding" → Willison 于 2025 年 10 月提出 "Vibe Engineering" → Karpathy 于 2026 年 2 月建议 "Agentic Engineering" → Osmani 与 Willison 于 2026 年 2 月分别发表文章将其确立为行业术语。
3. **Agentic Engineering ≠ Vibe Coding**：前者是以工程纪律为核心、以 AI 代理为执行力的专业开发方式；后者是快速原型阶段的"YOLO"式开发。
4. **测试是区分两者的关键**：拥有可靠测试套件是 Agentic Engineering 的核心使能条件。
5. **该领域学术研究极其活跃**：arXiv 上有超过 1,350 篇相关论文，涵盖安全、评估、多代理协作、软件工程等多个方向。

## 阅读建议

- 如果你是初次接触这个话题，建议按 `00` → `01` → `06` 的顺序阅读
- 如果你想深入了解技术细节，重点阅读 `02` 和 `03`
- 如果你正在选型工具，直接阅读 `05`
- 如果你想追踪最新动态，关注 `07`
