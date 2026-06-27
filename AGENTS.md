# AGENTS.md

本仓库保存个人 Codex skills。修改时优先保持 skill 简洁、可复用、可验证。

## 工作规则

- 新增或更新 skill 时，先阅读相关 `SKILL.md` 和 `agents/openai.yaml`。
- 每个 skill 目录名必须与 `SKILL.md` frontmatter 的 `name` 一致。
- `SKILL.md` frontmatter 只保留 `name` 和 `description`。
- `description` 必须说明 skill 做什么，以及什么场景会触发它。
- `agents/openai.yaml` 的 `default_prompt` 必须显式包含 `$skill-name`。
- 不创建额外 README、安装指南、示例文件或模板，除非用户明确要求或 skill 功能确实依赖。
- 更新仓库内 skill 后，如用户要求同步，应复制到 `{codex_home}\skills\<skill-name>` 并对同步后的目录再次验证。

## 权限边界

- 读取文件、查看 git 状态、查看 diff、搜索代码、生成分析报告等只读操作不需要额外确认。
- 修改文件、改代码、更新 checklist、移动或删除文件、回滚文件等会改变工作区文件内容的操作，必须先说明范围和风险，并等待用户确认；如果用户已在当前请求中明确授权具体范围，可以直接执行。
- `git add`、`git commit` 和 `git push` 不视为修改文件；执行前必须复核范围，确保不混入无关改动、未确认改动、密钥、本地配置、缓存、日志或构建产物。

## 验证

新增或修改 skill 后，运行：

```powershell
py -3 -X utf8 '{codex_home}\skills\.system\skill-creator\scripts\quick_validate.py' '<skill-dir>'
```

如果验证脚本通过普通 `python` 因编码失败，使用 `py -3 -X utf8`。

## 提交

- 提交前运行 `git status --short` 和 `git diff --stat`。
- 只暂存本次任务相关文件。
- 暂存后复核 `git diff --cached --stat` 和 `git diff --cached --name-only`。
- 不提交密钥、token、私钥、`.env`、本地配置、缓存、日志或构建产物。
- 提交信息默认使用中文，格式优先为 `<type>(<scope>): <summary>` 或 `<type>: <summary>`。
