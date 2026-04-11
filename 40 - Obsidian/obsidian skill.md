---
title: "Obsidian 必装 Skills 整理"
date: 2026-04-09
tags:
  - Obsidian
  - Skills
  - AI工具
  - 工具配置
aliases:
  - Obsidian Skills
  - obsidian skill
source: "https://file.notion.so/f/f/59598ac9-9811-819f-adcd-000369d5acbf/b0337e3a-de22-4396-a1fb-a98e7a77f4bb/Obsidian%E5%BF%85%E5%A4%87Skills.md"
---

# Obsidian 必装 Skills 整理

> [!tip] 一句话结论
> Obsidian + AI Agent 的核心是让 AI 能读懂、写出、可视化你的笔记。Skills 是 AI 操作 Obsidian 的"驱动程序"，选对 Skill 比什么都重要。

---

## 一、Skills 全览速查

| Skill | 作者 | 功能 | 推荐度 |
| :---- | :--- | :--- | :----: |
| **defuddle** | kepano | 网页正文提取 → 干净 Markdown，节省 Token | ✅ |
| **obsidian-cli** | kepano | AI 直接调用 Obsidian 官方 CLI，增删改查笔记 | ✅ |
| **obsidian-bases** | kepano | 创建 `.base` 文件，生成类 Notion 数据库视图 | ✅ |
| **obsidian-markdown** | kepano | 编写符合 Obsidian 规范的增强 Markdown | ⚠️ |
| **json-canvas** | kepano | 创建 `.canvas` 白板文件（基础版） | ❌ |
| **obsidian-canvas-creator** | Axton | 加强版 Canvas，自动计算坐标防重叠 | ✅ |
| **mermaid-visualizer** | Axton | 文本 → Mermaid 架构图/流程图 | ✅ |
| **excalidraw-diagram** | Axton | 文本 → 手绘风格 Excalidraw 图表 | ✅ |
| **obsidian-skill** | OpenClaw 官方 | 直接文件 I/O（已过时，不推荐） | ❌ |
| **tutor-skills** | Choi Wontak | 文档/代码库 → 结构化知识库 + 交互测验 | ✅ |
| **scholar-skill** | EESJGong | 论文深度解析 → Obsidian 知识卡片（重型） | ✅ |

> [!warning] obsidian-skill 已过时
> OpenClaw 官方的 `obsidian-skill` 使用暴力文件 I/O，Token 消耗大，在 `obsidian-cli` 发布后没有继续使用的理由。

---

## 二、各 Skill 详解

### defuddle

**功能**：抓取网页核心正文，剔除广告/导航栏，输出干净 Markdown。支持 YouTube 字幕（调用官方 API）。

**依赖**：
```bash
npm install -g defuddle
```

**触发方式**：发送 URL + 要求阅读/分析/总结时自动触发。

**示例提示词**：
```text
提取这个网页的正文，转成干净的 Markdown 格式：[URL]
```

> [!note] 注意
> 对标准 HTML 网页效果最好；需要登录或纯动态渲染的 SPA 效果受限。

---

### obsidian-cli

**功能**：让 AI 直接调用 Obsidian 官方 CLI，实现笔记增删改查、插件调试。

**配置步骤**：
1. Obsidian 版本 ≥ 1.12
2. 设置 → 常规 → 开启「命令行界面」
3. 确认注册到系统 Path
4. 保持 Obsidian 客户端运行

**验证**：
```bash
obsidian daily
```
成功则自动生成今日日记。

---

### obsidian-bases

**功能**：创建 `.base` 文件，生成类 Notion 数据库动态视图，支持过滤、公式计算、结构化展示。

**依赖**：如需地图视图，需安装 Maps 社区插件。

**支持视图类型**：`table` / `cards` / `list` / `map`

**示例提示词**：
```text
写一个 .base 文件来管理我的项目笔记，筛选所有带 #project 标签的文件，
用表格显示项目名称、截止日期和剩余天数。
```

---

### obsidian-markdown

**功能**：编写符合 Obsidian 规范的增强 Markdown，支持双向链接、内容嵌入、Callout、结构化属性。

**示例提示词**：
```text
根据这段会议记录生成一份 Obsidian 笔记。要求在顶部包含日期和参会人属性，
使用双向链接关联到「项目 A」笔记，并把关键决策点用 important 类型的提示框标注。
```

> [!tip] 进阶用法
> 可以把个人 Obsidian 格式偏好加入 Skill 中，让 AI 写出的笔记完全符合你的风格。

---

### obsidian-canvas-creator（推荐替代 json-canvas）

**功能**：内置径向布局（MindMap）和自由排版（Freeform）算法，自动计算节点坐标、处理连线、动态调整节点尺寸。

**示例提示词**：
```text
把这篇 Markdown 知识笔记转换成 mindmap 格式的 Obsidian Canvas。
```

**与 json-canvas 的区别**：

| 对比项 | json-canvas | obsidian-canvas-creator |
| :----- | :---------- | :---------------------- |
| 侧重点 | JSON 语法正确性 | 排版策略 + 坐标计算 |
| 自动化 | 机械摆放节点 | 自动防重叠、间距美观 |
| 布局模式 | 无 | MindMap / FreeForm |

---

### mermaid-visualizer

**功能**：文本逻辑 → 专业 Mermaid 架构图/流程图，内置 Obsidian 渲染引擎语法纠错。

**示例提示词**：
```text
根据这段关于软件开发生命周期的描述生成一个横向 Mermaid 流程图，
要求包含不同子图来区分开发环境和测试环境。
```

---

### excalidraw-diagram

**功能**：文本逻辑 → 手绘风格 Excalidraw 图表。

**依赖**：需安装并启用 Excalidraw 插件。

**示例提示词**：
```text
用 Excalidraw 画一个 AI 智能体的工作流图，按照感知、思考、行动的顺序排列，
保存为 .excalidraw 文件。
```

---

### tutor-skills

**功能**：两个 Skill 构成完整学习闭环：
- `tutor-setup`：文档/代码库 → 结构化 Obsidian 知识库（带双链、MOC、复习题）
- `tutor`：读取进度数据，生成交互式测验，追踪知识薄弱点

**依赖**：Claude Code / OpenCode 等 AI Agent 工具。

**使用方式**：
```bash
/tutor-setup   # 在工作目录触发构建
/tutor         # 在已有 StudyVault 目录触发复习
```

**自动模式识别**：
- 发现 `package.json` / `pom.xml` → 代码库模式
- 只有 PDF/纯文本 → 文档模式

> [!warning] Token 消耗
> 代码库模式会递归读取大量源文件，短时间内消耗大量 Token。

---

### scholar-skill

**功能**：论文深度解析工作流，L1/L2/L3 分级阅读，输出双链卡片、MOC、反思报告到 Obsidian。

**依赖（重型）**：
- 本地 Python 环境 + Obsidian 客户端
- OpenClaw 框架
- 必须 Skill：`obsidian-direct`、`arxiv-watcher`、`durable-task-runner`
- 可选：`tavily`、`pdf`、`academic-research-hub`

**示例提示词**：
```text
获取这篇文献 ArXiv:2407.19354 并进行处理。
先做 L1 快速评估，如果判定为 P0 优先级，则在后台启动 L3 深度阅读。
完成后将知识树更新推送到我的 Obsidian 对应目录。
```

**特殊机制**：
- L3 深度阅读为 **2.5 小时异步挂机任务**，依赖 `durable-task-runner` 断点续传
- 内置周期性反思触发器（周末/月末）
- **Human in the loop**：发现新论文推翻旧结论时，不直接覆写，而是生成确认单放入 `0-Inbox`

> [!danger] 风险预警
> - **财务风险**：L3 循环 + 高频 RAG 检索，挂载商用前沿模型单篇深读账单可能极高
> - **数据风险**：`obsidian-direct` 使用暴力文件 I/O，多端同步期间易引发文件冲突/内容丢失。**强烈建议在独立测试库运行，并开启 Git 快照**

---

## 三、核心插件

### claudian
- **用途**：Obsidian 第三方插件，适配 Claude Code
- **仓库**：`YishenTu/claudian`（暂未上架官方市场）

**安装方式（推荐 BRAT）**：
1. 插件市场安装 BRAT
2. 设置 → BRAT → Add Beta plugin → 输入 `YishenTu/claudian`
3. 在第三方插件列表中启用

**配置**：
```bash
# 替换为兼容 Anthropic 接口的模型（如 DeepSeek）
ANTHROPIC_BASE_URL=https://open.bigmodel.cn/api/anthropic
ANTHROPIC_API_KEY=你的API Key
ANTHROPIC_DEFAULT_OPUS_MODEL=GLM-5
```

---

### obsidian-agent-client
- **用途**：适配主流 AI Agent（Claude Code / Codex / Gemini CLI / OpenCode / Qwen Code）
- **仓库**：`RAIT-09/obsidian-agent-client`（暂未上架官方市场）

**配置示例（以 OpenCode 为例）**：
1. 设置 → Add Custom Agent，ID 和 Name 填 `OpenCode`
2. `where opencode` 查看安装路径，填入 Path
3. Arguments：
```bash
acp
--cwd
D:\Obsidian Vault\MyObVault
```

---

## 四、选用建议

```
可视化需求
├── 思维导图/Canvas → obsidian-canvas-creator（优先）
├── 流程图/架构图  → mermaid-visualizer
└── 手绘风格图表   → excalidraw-diagram

笔记操作
├── 增删改查笔记   → obsidian-cli
├── 数据库视图     → obsidian-bases
└── 规范化格式     → obsidian-markdown

内容获取
└── 网页正文提取   → defuddle

学习/研究
├── 文档/代码学习  → tutor-skills
└── 论文深度解析   → scholar-skill（重型，谨慎使用）
```

#Obsidian #AI工具 #Skills #工具配置