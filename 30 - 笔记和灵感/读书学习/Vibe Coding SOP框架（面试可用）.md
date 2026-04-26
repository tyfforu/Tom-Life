# Vibe Coding SOP 框架（面试可用）

> 来源：小红书 @GerryVood 的AI PM日记 · Episode #11
> 核心价值：30+ 同学 workshop 亲测 · 30-45 分钟造出 prototype · 大幅省 token

---

## 5 步速览

```
📋 → 🎨 → 🏗️ → 🔄 → 🎓
```

| 步骤 | 名称 | 设计哲学 | 推荐 Skill |
|------|------|---------|-----------|
| Step 1 | Clarify Spec & Scope MVP | 苏格拉底式提问·AI帮你思考，不给方案 | superpowers |
| Step 2 | Explore 3+ Design Options | Design Thinking · Diverge before converge | frontend-design |
| Step 3 | Build Architecture & Implementation Plan | 先思考再动手·防 over design | superpowers |
| Step 4 | Build, Verify & Capture Learnings | 高度动态过程·checkpoint + memory.md | superpowers |
| Step 5 | Learn Back from the Code | Growth Mindset · 不做"知其然不知其所以然"的 PM | codebase-to-course |

---

## STEP 01 · Clarify Spec & Scope MVP
**厘清需求 + 定义 MVP 范围**

**设计哲学**：苏格拉底式提问 · AI 不是给你方案，是帮你思考

### 主要动作
1. ⭐ 先用 **3C 策略**把产品想法结构化描述给 AI：
   - **Context** — 产品目的 / 目标用户 / 使用场景
   - **Components** — 初始功能 / 核心 feature / 期待体验
   - **Criteria** — 性能 / 可扩展 / 安全 / 预算（例：所有产品必须有 groundtruth 可做 eval）
2. 然后让 AI 反过来问你 clarifying questions —— **一次只问一个**，逼你想清楚模糊的地方
3. 围绕延伸维度继续厘清：几类 persona / MVP 范围 / 业务+技术指标 / Corner cases / UI 风格
4. ⭐ **明确定义 MVP 范围** —— 这步定不清楚，后面架构会失控

### Prompt 示例
```
# PRODUCT IDEA — 3C
CONTEXT: Purpose / Users / Use cases
COMPONENTS: Initial features / Expected experience
CRITERIA: Performance / Security / Budget
           (e.g. outputs must have groundtruth for eval)

# INSTRUCTIONS
- Ask clarifying questions BEFORE writing PRD
- ONE question at a time

# PRD MUST INCLUDE
- Problem + metrics · Personas · MVP scope
- Technical considerations · Corner cases
```

### 面试注意事项
- 提前准备 1-2 分钟能讲清楚的 idea —— **直接用 3C 框架组织**（Context / Components / Criteria）最清晰
- 每个 clarifying question 只回答最核心的信息
- MVP 范围定小不定大 —— 面试就 30-45 分钟

---

## STEP 02 · Explore 3+ Design Options
**探索 3+ 设计方案**

**设计哲学**：Design Thinking · Diverge before converge  
落笔前先发散，发散够了再收敛

### 主要动作
1. 让 AI 一次生成 3+ **差异明显**的设计方向（不是一个方案的 3 个变体）：
   - **Simplicity-first**：极简、少交互
   - **Automation-first**：最大化 AI 主动性
   - **Gamification**：游戏化、高互动
2. 只生成 landing page 做风格探索 —— 不造完整原型。目的是快速对比视觉风格和信息组织
3. 快速对比后锁定一个方向进入下一步 —— 不再回头调 3 选 1 的过程

### Prompt 示例
```
Use the front-end design skill to explore UI/UX.
Only generating a landing page is OK — explore styles, don't build the prototype.
Exploration rules: All 3+ options must be clearly differentiated.
```

### 面试注意事项
- 别恋战 —— 每个方向看 2-3 分钟够了
- 跟面试官简短汇报 "我看过这 3 条路，选 X 因为…"，展示决策思考过程（加分点）
- 选完立刻向前，不要回头再改 3 选 1

---

## STEP 03 · Build Architecture & Implementation Plan ⚠️ 最易翻车
**做架构设计与实施方案**

**设计哲学**：先思考再动手 · 让 AI 有一次"出声思考"的机会

### 主要动作
1. 让 AI 先写 **architecture design doc + implementation plan**，明确说"不要直接写代码"
2. 架构要考虑的 **5 个维度**：
   - ⚡ Performance / 📝 Scalability / 🔒 Security
   - 🗄️ Data Modeling / 👁️ Observability
3. 不放心就兜底：让另一个 AI challenge 方案，或找信任的 engineer review
4. ⚠️ 在 prompt 里**显式加防 over design 咒语**：
   - "This is only a prototype" / "Frontend only" / "MVP scope only"
   - "DO NOT consider large-scale concurrency, multi-tenancy"
   - **说清楚不做什么，比说做什么更重要**

### Prompt 示例
```
Write architecture design + implementation plan based on PRD.
Consider: performance / scalability / security / data / observability.
Scope (interview): local only, no cloud, fully functional.
DO NOT over-design. This is a prototype.
```

### 面试注意事项
- ⚠️ **最易翻车步骤** —— 45 分钟里 over design 必崩
- Highlight 你考虑了哪 5 个维度 + 做了哪些简化（展示"我知道完整架构，做了取舍"）
- 不显式限制，AI 默认会按"云原生架构师"模式输出

---

## STEP 04 · Build, Verify & Capture Learnings
**动态构建 + 沉淀学习**

**设计哲学**：高度动态过程 · 持续对话 + 不断 course correct

### 主要动作
1. 把 plan **拆成小 task**，让 AI 逐个 build —— 不要一口气让它做完 5 个功能
2. 每完成一个 feature 就 **checkpoint 验证**：先测跑不跑 → 跑通再进下一个 → 错了立刻回溯
3. MVP 跑通后，让 agent 反思并写 **memory.md**，记录：
   - 你表达过的设计偏好（显式 + 隐式）
   - 一路修过的 bad assumptions
   - 你 pushback 过的 pattern / 下次要遵循的 convention
4. 把 memory.md 放进 project folder —— 下次 AI 直接参考。**越积累越值钱，是你 AI-native 工作流的护城河**

### Prompt 示例
```
BUILD: Work in small checkpoints. Verify each feature runs before moving on.
CAPTURE: Write memory.md — design preferences, bad assumptions fixed, patterns pushed back, conventions to follow next time.
```

### 面试注意事项
- 即使不真写 memory.md，也要口头告诉面试官"如果继续做，下次启动前我会把 learnings 记进 memory"
- 展示 **AI-native PM 思维**最好的时机
- 别让 AI 闷头一口气 build —— 面试官想看你"指挥"，不是"放手不管"

---

## STEP 05 · Learn Back from the Code
**反学架构与 tradeoff**

**设计哲学**：Growth Mindset · 不做"知其然不知其所以然"的 PM  
最大的风险不是 bug，是 AI 做对了你不知道 why

### 主要动作
1. **轻量版**：一个 prompt 让 AI 生成架构图 + 总结：
   - Key tradeoffs（为什么选 X 不选 Y）
   - 技术 insights · 风险管理
   - 如果有更多时间会怎么 redesign
   - 5 分钟能完成 · 适合面试临时救急
2. **进阶版**：codebase-to-course skill —— 把整个 repo 自动转成交互式 HTML 课程：
   - 代码 ↔ 纯英文对照 · 动画可视化架构
   - 交互式测验 · 术语 hover 提示
   - 专为 vibe coder 设计

### Prompt 示例（轻量版）
```
Generate an HTML architecture diagram for this product, then summarize: key tradeoffs (why X over Y), technical insights, risk management, and what you'd redesign with more time.
```

### 面试注意事项
- 面试官问"讲讲你的架构"时只说"我让 AI 写的"基本就结束了
- **能讲 tradeoff 的 PM** 比只讲功能的 PM 加分非常明显
- 面试临时版：用轻量 prompt 当场生成架构图，抱屏讲

---

## Skill 组合拳（全套 SOP 打通）

| Skill | 覆盖步骤 | 来源 |
|-------|---------|------|
| **superpowers** | Step 1 + 3 + 4（brainstorm · TDD plan · checkpoint · review） | github.com/obra/superpowers |
| **frontend-design** | Step 2（3+ 设计方向视觉风格探索） | github.com/anthropics/claude-code/tree/main/plugins/frontend-design |
| **codebase-to-course** | Step 5（把 repo 变成交互式课程） | github.com/zarazhangrui/codebase-to-course |

> skill 组合拳装完 · 5 步 SOP 全线打通

---

## 下期预告
**《我的 AI 编程工具 7 个 best practice + 实战技巧》**  
每天用 Cursor / Claude Code 喂养出来的 7 条最佳实践，都是踩过坑才摸出来的
