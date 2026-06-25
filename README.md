# My Skills

这是一个个人 Codex skills 仓库，用于保存可复用的本地 skill。

## Skills

### mygit-skill

通用 Git 提交流程和提交信息规范。用于检查本地改动、判断提交拆分、运行合适的提交前检查、安全暂存文件，或起草符合仓库约定的 Git 提交信息。

位置：`mygit-skill/`

### dev-checklist

通用开发 checklist 生成和维护流程。用于为软件功能、模块改造、工具链、UI、后端、游戏系统或重构任务创建阶段式 Markdown checklist，或更新已有 checklist 的完成状态、阶段总结、验证记录和后续开发项。

位置：`dev-checklist/`

### multi-agent-dev

多 agent 开发编排流程。用于主 agent 基于用户提供的 checklist/summary 文件调度 subagent 按阶段或子任务连续开发、逐项审核打勾并附证明、运行测试、打回修复，并使用 `mygit-skill` 提交代码改动。

位置：`multi-agent-dev/`

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
- 提交前只暂存本次变更相关文件。
