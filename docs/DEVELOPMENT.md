# 故事工坊（Story Workshop）- 开发概述

## 1. 项目简介

**故事工坊** 是一套面向 AI 写作代理的结构化内网文章写作工作流技能框架，核心写作风格为「冷静的温度」——在理性克制的叙事中传递真实的力量感。

项目将 AI 从"随意写作"工具转变为系统化写作伙伴，通过强制执行四阶段流程，产出结构清晰、论据充分、可发布级别的内网文化故事、人物访谈和技术复盘文章。

> **致谢：** 本项目的多阶段流水线架构、硬性门禁机制、子代理驱动写作等核心思路，借鉴自 [Document Superpowers](https://github.com/binbinao/document-superpowers) 的代码开发方法论。感谢 Document Superpowers 项目的启发——它证明了将软件工程的严谨性引入创作流程是完全可行的，故事工坊在此基础上针对中文内网写作场景做了深度适配。

**仓库地址：** `https://git.woa.com/ninojia/story-workshop.git`

**项目类型：** 纯文档型 AI Agent 技能框架（无代码逻辑、无构建系统）

---

## 2. 核心架构

### 2.1 多阶段写作流水线

```
💡 素材分析 → [📝 访谈提纲] → 📋 写作规划 → ✍️ 内容执行 → 🔍 文章审校
```

每个阶段由独立的技能模块（GUIDE.md）驱动，并设有严格的阶段门禁（Gate），前一阶段未完成且未获用户批准，不能进入下一阶段。访谈提纲阶段在素材不足时自动触发。

### 2.2 技能模块总览

| 阶段 | 技能模块 | 路径 | 职责 |
|------|---------|------|------|
| 1. 素材分析 | `brainstorming` | `skills/brainstorming/GUIDE.md` | 分析素材、提取信息、评估充分度，产出写作简报 |
| 1.5 访谈提纲 | `interview-outline` | `skills/interview-outline/GUIDE.md` | 素材不足时自动触发，识别信息缺口，生成差异化访谈问题 |
| 2. 写作规划 | `writing-article-plan` | `skills/writing-article-plan/GUIDE.md` | 逐节拆分大纲，确定字数/要点/证据/过渡 |
| 3a. 内容执行（批量） | `executing-article-plan` | `skills/executing-article-plan/GUIDE.md` | 按批次（2-3 节）写作，带人工检查点 |
| 3b. 内容执行（子代理） | `subagent-driven-writing` | `skills/subagent-driven-writing/GUIDE.md` | 每节生成独立子代理，两阶段审查（规格+质量） |
| 4. 文章审校 | `reviewing-article` | `skills/reviewing-article/GUIDE.md` | 三轮审阅：内容 → 表达 → 规范 |

### 2.3 技能调用链

```
SKILL.md（入口）
  └─→ brainstorming/GUIDE.md
        └─→ writing-article-plan/GUIDE.md
              ├─→ executing-article-plan/GUIDE.md（批量模式）
              └─→ subagent-driven-writing/GUIDE.md（子代理模式）
                    └─→ reviewing-article/GUIDE.md
```

---

## 3. 项目结构

```
story-workshop/
├── README.md                              # 用户文档（安装、使用、写作风格、故障排除）
├── SKILL.md                               # 技能入口点（触发条件、阶段概览、「冷静的温度」准则）
├── docs/
│   ├── SUMMARY.md                         # 项目总结（统计、设计原则、后续计划）
│   ├── DEVELOPMENT.md                     # 本文件 - 开发概述
│   └── examples/
│       └── typescript-blog-post.md        # 完整的四阶段写作示例
└── skills/
    ├── brainstorming/
    │   └── GUIDE.md                       # 阶段 1：素材分析与写作简报
    ├── interview-outline/
    │   └── GUIDE.md                       # 阶段 1.5：访谈提纲生成（素材不足时）
    ├── writing-article-plan/
    │   └── GUIDE.md                       # 阶段 2：详细大纲规划
    ├── executing-article-plan/
    │   └── GUIDE.md                       # 阶段 3a：批量执行模式
    ├── subagent-driven-writing/
    │   └── GUIDE.md                       # 阶段 3b：子代理驱动模式
    └── reviewing-article/
        └── GUIDE.md                       # 阶段 4：多轮系统审阅
```

### 文件统计

| 文件 | 行数 | 大小 |
|------|------|------|
| `examples/typescript-blog-post.md` | 632 | 15.7 KB |
| `skills/reviewing-article/GUIDE.md` | 382 | 9.3 KB |
| `README.md` | 336 | 10.3 KB |
| `skills/subagent-driven-writing/GUIDE.md` | 276 | 7.8 KB |
| `SKILL.md` | 273 | 8.0 KB |
| `SUMMARY.md` | 263 | — |
| `skills/writing-article-plan/GUIDE.md` | 214 | 5.6 KB |
| `skills/executing-article-plan/GUIDE.md` | 201 | 5.3 KB |
| `skills/brainstorming/GUIDE.md` | 137 | 5.4 KB |
| **总计** | **~2,714** | — |

---

## 4. 技术栈与依赖

| 维度 | 详情 |
|------|------|
| **语言** | 纯 Markdown |
| **构建工具** | 无 |
| **依赖管理** | 无（无 package.json / requirements.txt） |
| **测试框架** | 无（通过真实写作项目手动验证） |
| **CI/CD** | 无 |
| **版本控制** | Git，`main` 分支 |
| **目标平台** | WorkBuddy、Cursor、Claude Code 等 AI Agent 技能平台 |

### 技能文件格式

每个技能模块（GUIDE.md）采用统一结构：

- **YAML Frontmatter**：定义 `name`（技能名称）和 `description`（触发描述）
- **Markdown 正文**：流程说明、检查清单、模板
- **DOT 语法流程图**：可视化阶段流转逻辑

---

## 5. 核心设计原则

### 5.1 「冷静的温度」写作风格

这是故事工坊的核心写作哲学，10 条准则贯穿全流程：

1. **用动词推进叙事** — 让主语做事，不要让名词堆砌
2. **数据即论据** — 关键节点必须有数字锚定
3. **场景 > 形容词** — 用具体场景代替空洞修饰
4. **一段一事** — 每段只推进一个信息点
5. **克制使用感叹号** — 全文最多 1 个
6. **禁用空洞套话** — "赋能""抓手""沉淀"等词一律替换
7. **对话还原现场** — 关键决策用原话呈现
8. **先写结论** — 每节首句即核心信息
9. **删掉 30%** — 初稿完成后强制精简
10. **标题即承诺** — 读者看完标题就知道能得到什么

### 5.2 硬性门禁机制

头脑风暴阶段设有硬性门禁标记 `<HARD-GATE>`，强制要求：
- 不可跳过头脑风暴，即使"简单"文章也必须产出并获批写作简报
- 前一阶段未完成，不能进入下一阶段

### 5.3 一次一问

头脑风暴阶段每条消息只问一个问题，优先使用选择题，避免信息过载。

### 5.4 小节写作

每个写作小节控制在 150-400 字，既能充分展开一个论点，又可在 15-30 分钟内完成。

### 5.5 检查点机制

批量执行模式下每 2-3 个小节设人工检查点，子代理模式下每节两阶段审查（规格合规 + 内容质量）。

### 5.6 四轮分离式审阅

审阅阶段将关注点分离为四次独立扫描：

| 轮次 | 关注点 | 核心问题 |
|------|--------|---------|
| 第 1 轮 | 逻辑 | 论证是否连贯？有无逻辑谬误？ |
| 第 2 轮 | 证据 | 每个论点是否有数据/引用支撑？ |
| 第 3 轮 | 流畅度 | 段落过渡是否自然？节奏是否合适？ |
| 第 4 轮 | 润色 | 语法、格式、风格是否一致？ |

问题按严重程度分级：**严重**（阻断发布）→ **中等**（影响质量）→ **轻微**（可选修复）。

### 5.7 全过程文档化

每个阶段产出可追溯的工件文件：

| 阶段产出 | 文件路径 |
|----------|---------|
| 写作简报 | `docs/writing/YYYY-MM-DD-<topic>-brief.md` |
| 写作计划 | `docs/writing/YYYY-MM-DD-<topic>-plan.md` |
| 分节草稿 | `docs/writing/drafts/YYYY-MM-DD-<topic>/section-N-*.md` |
| 合并草稿 | `docs/writing/YYYY-MM-DD-<topic>-draft.md` |
| 审阅日志 | `docs/writing/YYYY-MM-DD-<topic>-review.md` |
| 最终成稿 | `docs/writing/YYYY-MM-DD-<topic>-final.md` |

---

## 6. 两种执行模式对比

| 维度 | 批量执行 | 子代理驱动 |
|------|---------|-----------|
| 粒度 | 每批 2-3 节 | 每节独立子代理 |
| 审查频率 | 批次结束后人工检查 | 每节两阶段审查 |
| 反馈循环 | 较长 | 紧密 |
| 适用场景 | 简单直接的文章 | 复杂/高风险内容 |
| 开销 | 较低 | 较高（频繁生成子代理） |
| 优势 | 连贯写作，减少中断 | 新鲜视角，易于纠偏 |

---

## 7. 安装与使用

### 安装方式

```bash
# WorkBuddy 平台
git clone https://git.woa.com/ninojia/story-workshop.git ~/.workbuddy/skills/story-workshop

# 其他 AI Agent 平台
# 将 skills/ 目录复制到对应的技能目录
```

### 触发方式

用户向 AI Agent 发出写作意图即可自动触发：

- "帮我写一篇关于 XX 项目的内网文章"
- "我想写一篇人物访谈"
- "帮我把这份访谈记录整理成文章"
- "写一篇项目复盘/技术分享文章"

### 快速流程

1. 用户提出写作主题，提供素材（访谈记录、文档等）
2. Agent 自动调用素材分析技能，逐一提问补充信息
3. 用户确认写作简报后，Agent 生成写作计划
4. 用户选择执行模式（批量 / 子代理），Agent 逐节写作
5. 草稿完成后自动进入四轮审阅
6. 产出可发布的最终成稿

---

## 8. Git 历史

| 提交 | 消息 |
|------|------|
| `9a76e03` | `feat: 初始化 story-workshop skill（含「冷静的温度」风格体系）` |

- **分支：** `main`（唯一分支）
- **远程仓库：** `origin` → `https://git.woa.com/ninojia/story-workshop.git`

---

## 9. 后续演进计划

### 第二阶段（可选增强）

- [ ] 文章类型模板（人物故事 / 项目复盘 / 技术分享 / 团队文化）
- [ ] 企业内部风格指南集成
- [ ] 多种发布格式导出（KM、公众号、内网平台）
- [ ] 素材自动摘要与关键信息提取
- [ ] 多作者协作模式

### 第三阶段（高级功能）

- [ ] 可读性评分与优化建议
- [ ] 标题 A/B 测试
- [ ] 历史文章风格学习
- [ ] 版本对比工具
- [ ] 与内网发布平台集成

---

## 10. 已知事项与注意点

| 事项 | 说明 |
|------|------|
| 无自动化测试 | 项目为纯文档，通过真实写作任务手动验证 |
| .gitignore 缺失 | 建议添加以排除不必要的文件 |

---

*文档更新日期：2026-03-19*
