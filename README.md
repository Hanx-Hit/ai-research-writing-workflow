# AI Research Writing Workflow

把学术论文写作中重复的「写草稿 → 润色 → 英转中核对 → 中转英 → 去 AI 味 → 审稿」流程，封装成一组 [Claude Code / Cursor Agent Skills](https://cursor.com/docs/context/skills)。既能**一键走完整条流水线**，也能**单独调用任意一个环节**。

Prompt 内容整理自社区项目 [Leey21/awesome-ai-research-writing](https://github.com/Leey21/awesome-ai-research-writing)（来自 MSRA、字节 Seed、上海 AI Lab 及北大/中科大/上交等一线科研人员的实战写作 prompt），本仓库在其基础上做了**工作流编排与自动分流**的封装。

## 它解决什么

原来每个 prompt 要手动复制、手动判断该用 LaTeX 版还是 Word 版、手动一步步串起来。现在：

- **一键全流程**：`/paper-workflow` 贴入草稿（或给出论文文件路径），自动 翻译/写作 → 润色 → 去 AI 味 → 逻辑检查 → 真实性核查 → 审稿，**关键阶段暂停让你确认**，最后产出三块结果：**修改方向 + 修改意见 + 中英文完全对照**。
- **单功能调用**：任何一步都能用 `/<skill 名>` 独立触发。
- **自动检测**：运行时自动判断输入是 LaTeX 还是 Word 纯文本、是中文还是英文，选用对应规则，无需手动指定。
- **文件模式**：除了贴正文，也可直接给 `.tex` / `.docx` / 数据文件路径——agent 用 Read 读入、处理后经确认用 Edit 写回原文件，不必来回手动复制粘贴（回写前会先征得同意，建议先备份/commit）。
- **真实性闸门**：专设一环核查引用是否真实、正文数字与实验表是否一致、有无 overclaim；核不准的引用只标 `[CITATION NEEDED]`，绝不替你编造。

## 安装

### 方式一：放到项目 / 全局 skills 目录

```bash
# 克隆本仓库
git clone <your-repo-url> ai-research-writing-workflow

# 用于某个项目：把 skills 拷到该项目的 .claude/skills/ 下
cp -r ai-research-writing-workflow/.claude/skills/* /path/to/your-project/.claude/skills/

# 或全局可用（对所有项目生效）
cp -r ai-research-writing-workflow/.claude/skills/* ~/.claude/skills/
```

Claude Code / Cursor 启动时会自动发现 `.claude/skills/`（Cursor 也读 `.cursor/skills/`）下的 skills。

### 方式二：OpenSkills（可选）

若你使用 [OpenSkills](https://github.com/numman-ali/openskills) 生态，将本仓库推到 GitHub 后可：

```bash
npx openskills install <your-github-username>/ai-research-writing-workflow
```

## 用法

### 一键全流程

在对话中输入 `/paper-workflow`，或直接说「走一遍论文流程」「帮我把这段草稿处理成投稿稿」，然后贴入草稿或给出论文文件路径即可。流程会在润色后、去 AI 味后、逻辑检查后、真实性核查后、审稿后各暂停一次，等你确认或微调，最后给出修改方向、修改意见与中英文逐段对照。

### 单功能调用

| Skill | 作用 |
|-------|------|
| `/paper-workflow` | ★ 总控：一键全流程，关键阶段暂停，输出中英对照 |
| `/paper-translate` | 翻译：中转英 / 英转中 / 中转中（自动按语言+格式分流） |
| `/paper-polish` | 表达润色：英文 LaTeX 深度润色 / 中文克制润色 |
| `/paper-dehumanize` | 去 AI 味：英文 LaTeX / 中文 Word + AI 高频词表 |
| `/paper-logic-check` | 逻辑红线审查：只报致命错误 |
| `/paper-verify` | 真实性闸门：核对引用真实性、数字一致性、overclaim |
| `/paper-review` | Reviewer 视角审稿：审稿报告 + 改稿策略（支持 PDF 或文本） |
| `/paper-resize` | 缩写 / 扩写（微幅 ±5–15 词） |
| `/paper-analysis` | 实验数据 → LaTeX 分析段落 |
| `/paper-caption` | 生成图 / 表的英文标题 |
| `/paper-figure` | 架构图 prompt 生成 + 实验绘图类型推荐 |

## 自动检测说明

- **格式**：文本含 `\cite{}` `\ref{}` `\begin{}` `$...$` 等标记 → 按 LaTeX 处理（保留命令、转义 `%`/`_`/`&`）；否则按 Word / 纯文本处理（不转义、不带 Markdown，便于直接粘贴 Word）。
- **语言**：按中英文占比判定。中文草稿在全流程中先翻译/重写为目标语言，并保留中文直译用于最终对照；英文初稿直接进入润色。

## 致谢与版权

本仓库中各 skill 内嵌的 **prompt 文本内容**衍生自 [Leey21/awesome-ai-research-writing](https://github.com/Leey21/awesome-ai-research-writing)，整理自 MSRA、字节 Seed、上海 AI Lab 及北大/中科大/上交等一线科研人员的实战写作 prompt。

- **版权归属**：这些 prompt 文本的版权归原作者所有。本仓库仅在其基础上做**工作流封装与编排**（skill 切分、自动分流、总控流水线、文件模式、真实性闸门等编排逻辑为本仓库原创）。
- **衍生版本锚点**：内容对齐上游 `main` 分支 commit [`c07628b`](https://github.com/Leey21/awesome-ai-research-writing/commit/c07628b453309a1fb131ee105b2f01190162bc6c)（2026-05-18）。上游后续更新不会自动同步到本仓库。
- **许可证说明**：截至上述锚点，上游仓库**未声明开源许可证**（即默认保留所有权利）。因此本仓库**不另行附加任何开源许可证**，也不对内嵌 prompt 文本主张授权。如需将本工作流用于再分发或商业用途，请先与上游原作者确认授权。若你是原作者并希望调整此处署名或授权方式，欢迎提 issue。

