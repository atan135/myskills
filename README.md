# My Skills

这是一个个人 Codex skills 仓库，用于保存可复用的本地 skill。

## Skills

### brainstorm

结构化头脑风暴流程。用于用户发起话题后，先按“目标-限定-范围-检验”补齐并确认提示词，再开始头脑风暴、分析、方案设计或评估。

位置：`brainstorm/`

### code-optimize

代码体检与重构诊断流程。用于按功能模块为主、Git 提交记录为辅来审查代码、文件、目录或模块，发现重复逻辑、硬编码、职责分层、耦合、命名、注释和可维护性问题，并生成分步优化路线图。

位置：`code-optimize/`

### dev-checklist

通用开发 checklist 生成和维护流程。用于为软件功能、模块改造、工具链、UI、后端、游戏系统或重构任务创建阶段式 Markdown checklist，或更新已有 checklist 的完成状态、阶段总结、验证记录、最终完成定义和后续开发项。

默认按任务复杂度生成 5 到 15 个阶段；如果 15 个阶段仍不足以覆盖需求，会先建议拆分为多个 checklist。

位置：`dev-checklist/`

### git-review

本地 Git 历史提交审查流程。用于按时间窗口逐 commit 审查本地仓库提交，发现 bug、缺陷和安全问题，并按严重程度输出审查报告。

位置：`git-review/`

### multi-agent-dev

多 agent 开发编排流程。用于主 agent 基于用户提供的提示词和 checklist/summary 文件调度 subagent 按阶段或子任务连续开发；执行前先返回执行方案并等待确认，确认后再更新进度、审核、测试、打回修复和提交代码。

位置：`multi-agent-dev/`

### mygit-skill

通用 Git 提交流程和提交信息规范。用于检查本地改动、判断提交拆分、运行合适的提交前检查、安全暂存文件，或起草符合仓库约定的 Git 提交信息。

位置：`mygit-skill/`

## Skill 结构

每个 skill 目录至少包含：

```text
skill-name/
├── SKILL.md
└── agents/
    └── openai.yaml
```

- `SKILL.md`：Codex 读取的主要 skill 说明，包含 YAML frontmatter 和正文规则。
- `agents/openai.yaml`：UI 元数据，包括显示名称、简短描述和默认提示。

## 验证

修改或新增 skill 后，运行：

```powershell
py -3 -X utf8 '{codex_home}\skills\.system\skill-creator\scripts\quick_validate.py' '<skill-dir>'
```

示例：

```powershell
py -3 -X utf8 '{codex_home}\skills\.system\skill-creator\scripts\quick_validate.py' '{skills_repo}\dev-checklist'
```

## 维护约定

- 新增 skill 时保持目录名与 frontmatter `name` 一致。
- 不新增无关 README、示例、模板或资源文件，除非 skill 本身确实需要。
- 修改 `SKILL.md` 后检查 `agents/openai.yaml` 是否仍然匹配。
- 如果需要同步到 Codex 本地 skill 目录，复制到 `{codex_home}\skills\<skill-name>` 后再次运行验证。
- 只读检查不需要额外确认；修改文件需要确认或明确授权。`git add`、`git commit` 和 `git push` 不视为修改文件，但执行前要复核范围。
- 提交前只暂存本次变更相关文件。
