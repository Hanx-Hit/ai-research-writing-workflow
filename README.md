# AI Research Writing Workflow

把学术论文写作中重复的「写草稿 → 润色 → 英转中核对 → 中转英 → 去 AI 味 → 审稿」流程，封装成一组 [Claude Code / Cursor Agent Skills](https://cursor.com/docs/context/skills)。既能**一键走完整条流水线**，也能**单独调用任意一个环节**。

Prompt 内容整理自社区项目 [Leey21/awesome-ai-research-writing](https://github.com/Leey21/awesome-ai-research-writing)（来自 MSRA、字节 Seed、上海 AI Lab 及北大/中科大/上交等一线科研人员的实战写作 prompt），本仓库在其基础上做了**工作流编排与自动分流**的封装。

## 它解决什么

原来每个 prompt 要手动复制、手动判断该用 LaTeX 版还是 Word 版、手动一步步串起来。现在：

- **一键全流程**：`/paper-workflow` 贴入草稿，自动 翻译/写作 → 润色 → 去 AI 味 → 逻辑检查 → 审稿，**关键阶段暂停让你确认**，最后产出三块结果：**修改方向 + 修改意见 + 中英文完全对照**。
- **单功能调用**：任何一步都能用 `/<skill 名>` 独立触发。
- **自动检测**：运行时自动判断输入是 LaTeX 还是 Word 纯文本、是中文还是英文，选用对应规则，无需手动指定。

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

在对话中输入 `/paper-workflow`，或直接说「走一遍论文流程」「帮我把这段草稿处理成投稿稿」，然后贴入草稿即可。流程会在润色后、去 AI 味后、逻辑检查后、审稿后各暂停一次，等你确认或微调，最后给出修改方向、修改意见与中英文逐段对照。

### 单功能调用

| Skill | 作用 |
|-------|------|
| `/paper-workflow` | ★ 总控：一键全流程，关键阶段暂停，输出中英对照 |
| `/paper-translate` | 翻译：中转英 / 英转中 / 中转中（自动按语言+格式分流） |
| `/paper-polish` | 表达润色：英文 LaTeX 深度润色 / 中文克制润色 |
| `/paper-dehumanize` | 去 AI 味：英文 LaTeX / 中文 Word + AI 高频词表 |
| `/paper-review` | Reviewer 视角审稿：审稿报告 + 改稿策略（支持 PDF 或文本） |
| `/paper-logic-check` | 逻辑红线审查：只报致命错误 |
| `/paper-resize` | 缩写 / 扩写（微幅 ±5–15 词） |
| `/paper-analysis` | 实验数据 → LaTeX 分析段落 |
| `/paper-caption` | 生成图 / 表的英文标题 |
| `/paper-figure` | 架构图 prompt 生成 + 实验绘图类型推荐 |

## 自动检测说明

- **格式**：文本含 `\cite{}` `\ref{}` `\begin{}` `$...$` 等标记 → 按 LaTeX 处理（保留命令、转义 `%`/`_`/`&`）；否则按 Word / 纯文本处理（不转义、不带 Markdown，便于直接粘贴 Word）。
- **语言**：按中英文占比判定。中文草稿在全流程中先翻译/重写为目标语言，并保留中文直译用于最终对照；英文初稿直接进入润色。

## 致谢

Prompt 原始内容来自 [Leey21/awesome-ai-research-writing](https://github.com/Leey21/awesome-ai-research-writing)，遵循其开源精神。本仓库仅做工作流封装与编排。
