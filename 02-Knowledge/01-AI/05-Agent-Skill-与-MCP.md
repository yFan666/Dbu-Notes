---
type: knowledge
status: reference
area: 知识
review: monthly
created: 2026-06-17
updated: 2026-06-23
---

# Agent Skill 与 MCP

> 来源：Write Your Future（AI_/Agent Skill.md + AI_/MCP.md）
> 整合日期：2026-06-17

---

## Agent Skill

> 让 AI 快速学会新技能。

Skill 定义了一种**封装 AI 工作流**的标准：开发者可以把复杂的任务指令、脚本和资源打包成一个**技能（Skill）**；作为用户，你只需要安装这些技能，AI 就能立刻学会这项本事，不用重复造轮子。

### 核心价值

- 把"重复教 AI 同一套流程"变成"一次封装、处处复用"
- 降低 AI 协作的上下文成本
- 让 AI 从"通用助手"变成"某领域专家"

参考：https://ai.codefather.cn/library/2013072104018264066

---

## MCP（Model Context Protocol）

MCP 是一个**开放标准**，可以让 AI 工具连接到各种外部工具和数据源。

> 简单来说，MCP 就像给 AI 装上了各种"插件"，让它能做更多事情。
> eg. 文件系统 MCP 让 AI 可以读写文件，GitHub MCP 让 AI 可以操作 GitHub 仓库。

### 常用的 MCP 服务

**网页相关**
- Firecrawl MCP
- Brave Search MCP：注重隐私保护的网络搜索
- Context7：获取最新的技术文档

**文件存储**
- COS MCP：操作腾讯云对象存储

### MCP 什么时候放进上下文？

MCP 工具按需加载。原则：
- AI 在执行需要外部能力的任务时，按场景调用对应 MCP
- 不必把所有 MCP 工具一次性塞进上下文，避免污染
- 优先使用稳定的官方/知名 MCP 服务

参考：https://ai.codefather.cn/library/2010974694076256258

---

## Skill 与 MCP 的关系

| 维度 | MCP | Skill |
|---|---|---|
| 定位 | 连接外部工具和数据源的**协议** | 封装 AI 工作流的**能力包** |
| 粒度 | 单个工具（搜索、文件、数据库） | 完整流程（需求执行、日报整理） |
| 复用 | 工具级复用 | 流程级复用 |
| 配合 | Skill 内部可以调用 MCP 工具 | MCP 提供"手"，Skill 提供"脑" |

> 一句话：MCP 让 AI **能做**更多事，Skill 让 AI **会做**特定事。
