# Toolmaker Agent

<p align="center">
  <a href="./README.md">English</a> | <a href="./README.zh.md">简体中文</a>
</p>

<p align="center"><strong><em>我们不关心你构建什么，我们关心你如何构建它。</em></strong></p>

## 1. 这是什么

Toolmaker Agent 是一个 Agentic AI SDLC 平台——人类与智能体基于同一套结构化数据模型协同工作。

它是一个单一、自包含的可执行文件：一个采用分层架构的 Go REST API，配合 SQLite 存储，以及直接内嵌进二进制文件的编译后 React 前端，用于管理**产品（Product）、特性（Feature）、需求（Requirement）以及 UML/4+1 视图系统设计图**，并配备一个 LLM 驱动的对话式智能体和一个 MCP 服务端，供 Claude Code/Codex/DeepSeek Harness 这类编码智能体使用。打开一个端口，就能获得完整的工作台——不需要单独部署前端，也不需要额外配置外部数据库。

## 2. 解决的问题

当下的编码智能体（Code Agent）和 Skills 类工作流，产出的大多是自由格式的 Markdown 文档——这里一份 spec，那里一份 plan，另一个角落再来一份 ADR——散落在项目里各种临时的目录结构下，彼此之间没有任何 schema 关联，除了 grep 之外也没有办法查询"跟 X 相关的所有需求有哪些"。Toolmaker Agent 给这些产出提供了一个真正能落地的地方——带修订版本、父子关系和语义检索的、类型化的 Product/Feature/Requirement/UML 记录，每一条都有稳定的 OID 可以定位，而不是又一份丢进 docs 目录、跟其他一切都毫无关联的 Markdown 文件。

这也是这份数据模型本身遵循软件工程标准开发流程来设计的原因——从 Product 到 Feature、到 Requirement、再到 UML/系统设计，对应的正是产品定义、特性拆解、需求梳理、架构设计这几个经典阶段，而且这个流程还会持续向后延伸。每个阶段的产出物都以格式化（类型化）的数据保存下来，而不是散落的自由文本——这样才能在团队成员之间、以及不同的智能体之间可靠地共享和复用，而不是变成某一次对话或者某个编码智能体运行结束之后就没人看得懂的私有产出。

## 3. 核心特性

- **Product / Feature / Requirement / UML 的 CRUD**，均带有乐观并发控制（optimistic concurrency）。

- **4+1 系统设计视图**以 [Mermaid](https://mermaid.js.org/) 图渲染。任意一张图都可以导出为 **PNG 或 SVG** 图片。支持以下 7 种图类型：

  | 图类型 | Mermaid 语法关键字 | 语法文档 |
  |---|---|---|
  | 用例图 | `usecase-beta` | [Use Case Diagram](https://mermaid.ai/open-source/syntax/usecase.html) |
  | 类图 | `classDiagram` | [Class Diagram](https://mermaid.ai/open-source/syntax/classDiagram.html) |
  | 架构图 | `architecture-beta` | [Architecture Diagram](https://mermaid.ai/open-source/syntax/architecture.html) |
  | 流程图 | `flowchart` | [Flowchart](https://mermaid.ai/open-source/syntax/flowchart.html) |
  | 顺序图 | `sequenceDiagram` | [Sequence Diagram](https://mermaid.ai/open-source/syntax/sequenceDiagram.html) |
  | 状态图 | `stateDiagram-v2` | [State Diagram](https://mermaid.ai/open-source/syntax/stateDiagram.html) |
  | ER 图 | `erDiagram` | [Entity Relationship Diagram](https://mermaid.ai/open-source/syntax/entityRelationshipDiagram.html) |

  用例图和架构图使用的是 Mermaid 的 `-beta` 语法，尚未定稿，未来版本可能变化。

  <sub>注：`usecase-beta` 太新（2026-09 才随 v12.0.0 发布），LLM 尚未见过这套语法，对话/MCP 生成的用例图常混入 PlantUML 式错误写法，建议手动在 UI 中编写/校对。</sub>

- **LLM 对话智能体**，由 [trpc-agent-go](https://github.com/trpc-group/trpc-agent-go) 驱动，让你可以用"聊"的方式生成一个产品/特性/需求/UML 图，而不必去填表单：
  - 每次写操作都走提议-确认工具调用流程，模型永远不会在没有人工介入的情况下修改数据。
  - 持久化、按会话保存的历史记录，一旦超过阈值会自动**摘要**。
  - 可插拔的多供应商配置——OpenAI、Anthropic、Gemini、DeepSeek、Ollama、LM Studio、混元（Hunyuan）、Moonshot AI。
  - SSE 流式响应。

- **MCP 服务端**——使用官方的 [MCP SDK](https://github.com/modelcontextprotocol/go-sdk) 实现，以 Streamable HTTP transport 暴露 20 个工具，让 Claude Code/Codex/DeepSeek Harness 这样的编码智能体可以直接在终端里管理同一份数据——不需要确认这一步。

- **RAG / 语义检索**——页头的全局搜索框（默认搜索整个组织下的所有产品），以及一个对话智能体和 MCP 客户端都能用的语义搜索工具，可以按*语义*（"查找和 X 相似的需求"）而不仅仅是精确关键词/OID 匹配来查找实体。每次创建/更新都会异步重新生成该实体内容的向量。

## 4. 安装与配置

Toolmaker Agent 以六份预编译、自包含的二进制文件发布——每个平台/架构组合各一份——托管在公开仓库 [phcp-tech/toolmaker-agent](https://github.com/phcp-tech/toolmaker-agent) 的 Releases 页面。

### 4.1. 下载

| 平台 | 架构 | 文件名 |
|---|---|---|
| Windows | x64 | `toolmaker-agent-windows-amd64.exe` |
| Windows | ARM64 | `toolmaker-agent-windows-arm64.exe` |
| Linux | x64 | `toolmaker-agent-linux-amd64` |
| Linux | ARM64 | `toolmaker-agent-linux-arm64` |
| macOS | Intel | `toolmaker-agent-darwin-amd64` |
| macOS | Apple Silicon（M 系列芯片） | `toolmaker-agent-darwin-arm64` |

去 [Releases](https://github.com/phcp-tech/toolmaker-agent/releases) 页面下载跟自己机器匹配的那一份。

### 4.2. 安装与启动

这个二进制是完全自包含的（前端已经内嵌），放到任意目录都行——没有安装程序，也不需要另外部署任何东西。

- **Windows**：双击运行，或者在终端里执行。
- **Linux/macOS**：先加上可执行权限，再运行：
  ```bash
  chmod +x toolmaker-agent-linux-amd64   # 换成你实际下载的文件名
  ./toolmaker-agent-linux-amd64
  ```

首次启动会自动建好 SQLite 数据库（不需要手工执行任何建表步骤），默认监听 `http://localhost:8080`。它的配置/数据库/日志存放在一个跟可执行文件所在目录无关的、全机器共享的目录里——所以从不同目录跑多份、或者升级到新版本二进制，读写的都是同一份数据：

| 平台 | 数据目录 |
|---|---|
| Windows | `%ProgramData%\phcp\toolmaker-agent` |
| macOS | `/Library/Application Support/phcp/toolmaker-agent` |
| Linux | `/var/lib/phcp/toolmaker-agent` |

### 4.3. 配置 LLM 供应商

打开 `http://localhost:8080`，进入**设置 → LLM**。新增一条供应商配置——从内置选项（OpenAI、Anthropic、Gemini、DeepSeek、Ollama、LM Studio、混元、Moonshot AI、Qwen、GLM、MiniMax）里选一个，或者选 **自定义（兼容 OpenAI）** 来接入其他任何遵循 OpenAI 协议的服务——填入模型名和相应凭证，保存并设为启用，这一步是对话智能体和所有工具调用功能能正常工作的前提。

如果不想依赖任何云端服务、希望模型完全跑在本机——选 **Ollama** 或 **LM Studio** 即可：两者都是本地运行的模型服务器，不需要 API Key，按各自文档在本机装好、跑起来之后，把 Base URL 指向本机地址就行（Ollama 默认是 `http://localhost:11434`，LM Studio 默认是 `http://localhost:1234/v1`）。这意味着聊天、工具调用、MCP 这些功能都可以完全离线运行，不需要把任何数据发到公网上的第三方服务。

### 4.4. （可选）配置向量化服务以启用语义检索

如果还想启用 RAG/语义检索，去**设置 → Embedding**，配置一个向量化供应商（同样兼容 OpenAI 协议，比如 `Qwen text embedding`）。这是跟上面 LLM 配置完全独立的另一项设置。跳过这一步不会影响其他任何功能——Product/Feature/Requirement/UML 的 CRUD、对话、MCP 全部照常可用，只是语义检索这一项用不了。

### 4.5. MCP 服务端

把这个服务端注册进一个支持 MCP 的客户端（其他 Code Agent 请根据文档进行配置 MCP Client）：

- **Claude Code**：
  ```
  claude mcp add --transport http toolmaker-agent http://127.0.0.1:8080/agtapi/v2/mcp
  ```
- **Codex**：
  ```
  codex mcp add toolmaker-agent --url http://127.0.0.1:8080/agtapi/v2/mcp
  ```
- **DeepSeek Harness（DSH）**：借助 `@deepseek-ai/dsh-mcp-client` 插件，在 `$DSH_HOME/cordis.patch.yml`（对全部 profile 生效）或某个 profile 自己的 `$DSH_HOME/profiles/<name>/cordis.patch.yml` 里加一条配置：
  ```yaml
  - id: mcp-toolmaker-agent
    name: '@deepseek-ai/dsh-mcp-client'
    config:
      serverName: toolmaker-agent
      transport: streamable-http
      url: http://127.0.0.1:8080/agtapi/v2/mcp
  ```

### 4.6. 开始使用

1. 创建一个 **Product**。
2. 在它下面新建 **Feature** 和 **Requirement**——手动操作、用对话、或者用 MCP。
3. 打开**系统设计**，给某个 4+1 视图新建一张 **UML** 图。

这就是本次发布覆盖的完整核心流程。

### 4.7. 语义检索

有三种方式可以访问同一个底层向量索引，各自的检索范围不同：

| 入口 | 范围 | 说明 |
|---|---|---|
| 对话智能体工具 | 仅当前对话所在的产品 | 直接执行的工具，模型自己不能选择别的产品 |
| MCP 工具 | 单个产品，通过必填的 `productOid` 指定 | 与对话工具共用同一段 handler 逻辑 |
| /search API | 默认整个组织下的所有产品；可选传 `productOid` 缩小到单个产品 | 支撑页头的搜索框；因为命中结果可能来自任意产品，返回结果里带有 `productOid` |

三者都会返回每条命中结果的类型（`product`/`feature`/`requirement`/`uml`）、OID、名称和相关性得分——拿到 OID 之后，再通过对应的查询接口或详情面板查看完整实体。

## 5. 三种管理数据的方式

Toolmaker Agent 里的每一个实体——Product、Feature、Requirement、UML 图——都可以通过三个独立的入口创建、更新、删除，背后依托的是同一个 service 层和同一份数据库。选哪一种取决于当下的场景：填表单、用自然语言描述你想要什么，或者交给一个编码智能体去做。

### 5.1. 手动操作——网页 UI

普通的表单和详情面板：点击"+ 创建"、在字段里输入、点保存。全程没有任何 AI 参与——这是其余每一种模式都建立在其上的基础 CRUD 体验。

下面这段录屏创建了一个带正文内容的 Product、一个 Feature、两条 Requirement（并删除其中一条）、一张位于 Process 视图下的真实 UML 时序图，最后以一次语义检索收尾，直接跳转到匹配的 Feature。

![手动 CRUD 演示](docs/images/manual-crud-demo.gif)

### 5.2. LLM 对话——提议、确认、完成

页头的对话面板用自然语言操作同一套实体。每一次写操作都会走**提议 → 确认**流程：模型提出一个操作，你能看到它具体要做什么，点击"确认"之前不会写入任何数据。

举个例子，假设当前产品是 **Human Resource System**，引用一个 Feature 或 Requirement 时，可以用它的**名称**、它的 **OID**（比如 `FEA#24`），或者两个一起说，模型都能准确解析到对应的记录：

> "在特性 Leave Management 下新建一条需求，名称叫『请假单需要经理审批才能生效』——员工提交请假单后，需要对应的经理审批通过才能生效。"

模型会调用工具，把它推断出的每个字段都列在确认卡片里，只有你点击**确认**之后，新建的这条 Requirement（`REQ#87`）才会真正写入。接着在同一个对话里，这次直接用编号：

> "把 REQ#87 的优先级改成高。"

同样会走一遍这套流程，同样先给你看确认卡片，确认之前不保存任何东西。

对话也不一定只能是一次性的单条指令。你完全可以先跟模型充分讨论一个想法——范围、边界情况、取舍——等讨论真正收敛之后，再让模型把整段对话一次性整理成一个 Feature 及其下的 Requirement。从这个意义上说，Toolmaker Agent 也起到了长期存储的作用——对话本身受模型上下文窗口限制、终究会过去，但讨论出的结论一旦确认，就变成了结构化、可查询的记录，不会随着聊天记录被遗忘。

下面这段录屏用对话的方式跑了一遍和手动演示完全相同的场景——包括模型在必填字段（某条 Requirement 的正文内容）缺失时主动追问，等你回答之后再提出写操作。

![LLM 对话 CRUD 演示](docs/images/llmchat-crud-demo.gif)

### 5.3. MCP——让编码智能体直接操作同一份数据

把这个服务端注册进一个支持 MCP 的客户端（比如 Claude Code/Codex/DeepSeek Harness，它就能直接从终端创建、查询、更新、删除完全相同的实体。MCP 工具会立即执行——没有确认弹窗这一步，因为调用方本身就是正在操作智能体的开发者。

举个例子，Claude Code 注册好 MCP 服务端之后，同样的灵活性也带到了终端里：

> "在产品 Human Resource System 下，特性#24 里新建一条需求：『请假单需要经理审批才能生效』。"

Claude Code 会按名称解析出这个 Feature，调用工具，带上它从这句话里推断出的 `productOid`/`featureOid`/`name`/`content`，这条 Requirement 立刻就建好了——没有确认弹窗这一步，因为 MCP 工具会立即执行。

同样的思路也适用于更广泛的 MCP 场景：跟 Claude Code 进行一次很长的方案讨论，一旦有了结论，一次工具调用就能把它变成一条持久记录，供以后任何队友、或者另一个智能体查询。

下面这段录屏是一次真实的 Claude Code 会话，端到端地发起 MCP 工具调用：创建一个 Product、一个 Feature 和一条 Requirement（同样会在缺少必填字段时暂停并追问——这次是智能体在终端里直接问*你*），把它们查询回来，删除一条 Requirement，再创建一张 UML 时序图。

![MCP CRUD 演示](docs/images/mcp-crud-demo.gif)



## 6. 技术栈

| 层 | 技术 |
|---|---|
| 后端语言/运行时 | Go 1.26 |
| 公共库 common-library-golang |https://github.com/phcp-tech/common-library-golang | 
| HTTP 框架 | [Gin](https://github.com/gin-gonic/gin) |
| 数据库 | 通过 [dbsqlx](https://github.com/vinovest/sqlx) 使用 SQLite（原生 SQL，无 ORM）；schema 定义在 `config/schema_sqlite.sql` 中并据此建库 |
| Agent/LLM 编排 | [trpc-agent-go](https://github.com/trpc-group/trpc-agent-go) |
| RAG / 向量检索 | `trpc-agent-go` 的 `knowledge/embedder`（兼容 OpenAI 协议的 embedding，包括 DashScope）+ `knowledge/vectorstore/inmemory` |
| MCP | 官方 [modelcontextprotocol/go-sdk](https://github.com/modelcontextprotocol/go-sdk)，Streamable HTTP transport |
| 鉴权/策略 | [Casbin](https://github.com/apache/casbin) |
| CLI | [Cobra](https://github.com/spf13/cobra) |
| 前端 | React 19、TypeScript、Vite |
| 前端状态管理 | [TanStack Query](https://tanstack.com/query) v5 |
| 前端路由 | react-router-dom v7 |
| 样式 | Tailwind CSS |
| 图表 | [Mermaid](https://mermaid.js.org/)（部分高级图表类型还用到 Cytoscape、KaTeX） |

## 7. 许可证

Apache License 2.0——详见 [LICENSE](./LICENSE)。
