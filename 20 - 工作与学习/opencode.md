  
## 1. 工具定义与竞品对比

**OpenCode** 是一个开源的 AI Agent 编程工具，本质是 **Claude Code（人送绰号Close Code）** 的社区开源替代方案。支持代码编写，还能通过加载 "Skill"（技能包）执行文件操作、数据分析等通用计算机任务。

| 维度 | OpenCode | Claude Code |
| :--- | :--- | :--- |
| **内核性质** | 开源社区驱动，代码透明 | Anthropic 闭源商业产品 |
| **扩展性** | 支持自定义 Skill，兼容 Anthropic 标准 | 官方预设能力为主 |
| **成本** | 免费（仅需支付底层 API 费用），支持本地模型 | 需订阅或按 Token 付费 |
| **数据隐私** | 数据流可控，适合敏感项目 | 数据经过 Anthropic 服务器 |

## 2. 安装部署方案

### 命令行版 (CLI)
适用于开发者，集成于终端环境，资源占用极低。
*   **源码/仓库**：[GitHub - anomalyco/opencode](https://github.com/anomalyco/opencode)
*   **安装命令**（需 Node.js 环境）：
```bash
npm install -g @anomalyco/opencode
```

安装完成后在终端输入 opencode 启动

### 桌面版 (Desktop APP)
适用于演示、非代码类任务处理，提供可视化交互界面。
*   **下载地址**：[OpenCode 官网下载](https://opencode.ai/download)
*   **支持平台**：macOS, Windows, Linux

## 3. 版本形态对比决策

| 特性 | 命令行版 (CLI) | 桌面客户端 (Desktop) |
| :--- | :--- | :--- |
| **核心优势** | 极速响应、支持管道操作、可编写 Shell 脚本联动 | 可视化预览、文件拖拽交互、适合演示录屏 |
| **适用人群** | 程序员、DevOps、自动化极客 | 不习惯命令行、编程经验少的用户 |
| **Obsidian 联动** | **强**（可通过插件或 Shell 脚本深度集成） | **弱**（仅作为独立窗口使用） |

## 4. Agent Skill 加载机制

OpenCode 遵循 Anthropic 的 `SKILL.md` 标准。要扩展 AI 的能力（如读取 PDF、操作 Excel），需要将对应的技能文件放入指定配置目录。

**Skill 文件存放路径：**

*   **macOS / Linux**:
    ```bash
    ~/.config/opencode/skills/
    ```
*   **Windows**:
    ```powershell
    C:\Users\你的用户名\.config\opencode\skills\
    ```

> **操作提示**：在上述目录下新建文件夹（如 `excel-tool`），放入 `SKILL.md` 和配套脚本（如 `analyze.py`），重启 OpenCode 即可生效。

## 5. 应用场景
除了编程开发之外，还可以做很多事情：
*   **Obsidian 知识库重构**
    *   指向 Vault 目录，执行：“扫描 `/Inbox` 文件夹，提取所有关于 'RAG' 的笔记，合并成一篇新的永久笔记放入 `/Wiki`，保留原链接。”
*   **数据报表自动化**
    *   加载 Excel Skill 后，执行：“读取这个销售 CSV，计算各季度环比增长率，并用 Python 生成一张折线图保存到桌面。”

## 6. 常用命令

| 功能分类      | Slash 命令 (输入框)     | 快捷键 (CLI/Desktop) | 作用说明                                                  |
| :-------- | :----------------- | :---------------- | :---------------------------------------------------- |
| **核心流程**  | `/new` 或 `/clear`  | `Ctrl+x` + `n`    | **新建会话**：清空当前上下文，开始新任务。                               |
|           | `/exit` 或 `/quit`  | `Ctrl+x` + `q`    | **退出程序**：安全关闭 Agent。                                  |
|           | `/compact`         | `Ctrl+x` + `c`    | **压缩上下文**：将长对话总结为摘要，节省 Token 并防止遗忘。                   |
| **模式切换**  | `/plan` / `/build` | **`Tab`** (最常用)   | **切换模式**：在“规划模式”(只读/思考)与“构建模式”(写代码/执行)间切换。            |
| **时光机**   | `/undo`            | `Ctrl+x` + `u`    | **撤销**：回滚上一次 AI 的修改（基于 Git，非常安全）。                     |
|           | `/redo`            | `Ctrl+x` + `r`    | **重做**：恢复被撤销的操作。                                      |
| **上下文控制** | `@文件名`             | -                 | **引用文件**：模糊搜索并添加文件内容到 Prompt（如 `@utils.ts`）。          |
|           | `!命令`              | -                 | **Shell 直通车**：直接执行系统命令（如 `!npm install` 或 `!ls -la`）。 |
|           | `/editor`          | `Ctrl+x` + `e`    | **调用编辑器**：在你的默认编辑器（如 VS Code）中编写复杂的 Prompt。           |
| **系统与配置** | `/init`            | `Ctrl+x` + `i`    | **初始化**：在当前目录生成 `AGENTS.md`（项目规则文件）。                  |
|           | `/connect`         | -                 | **连接模型**：配置 API Key（OpenAI/Anthropic/Ollama 等）。       |
|           | `/models`          | `Ctrl+x` + `m`    | **切换模型**：在不同大模型之间无缝切换。                                |
| **桌面版专属** | -                  | `Cmd+Esc` (Mac)   | **全局唤醒**：类似于 Spotlight，快速呼出/隐藏 OpenCode 窗口。           |
|           | -                  | `Cmd+Opt+K`       | **插入引用**：在输入框中快速插入当前选中的文件路径。                          |