---
name: multi-agent-dev
description: 多 agent 开发编排流程。用于用户要求主 agent 基于用户提供的提示词和 checklist/summary 文件调度 subagent 按阶段或子任务连续开发；执行前必须先返回执行方案并等待用户确认，确认后再更新开始/结束时间、逐项审核并打勾附证明、运行测试、打回修复、使用 mygit-skill 提交代码且排除 checklist/summary。
---

# Multi Agent Dev

## 概述

使用本技能时，主 agent 负责编排、记录、审核、测试和提交；subagent 负责实际代码开发。主 agent 不接手实现，不用自己的代码修改替代 subagent 的工作。

在用户提供提示词和 checklist 文件后，先读取必要上下文并返回执行方案；必须等待用户明确确认后，才能开始更新 checklist、启动 subagent 或执行开发流程。

用户确认后，默认连续执行 checklist 中可识别的未完成子任务，直到全部完成、用户停止，或同一子任务连续 5 轮审核不通过。

## 角色边界

主 agent：

- 读取用户提供的 checklist 路径和相关背景。
- 识别下一个未完成阶段或子任务。
- 在派发 subagent 前更新该阶段或子任务的开始时间。
- 将明确背景、任务、范围、限制和验证要求发送给 subagent。
- 耐心等待 subagent 完成。
- 审核 subagent 的代码 diff，并运行或核对测试。
- 逐项审核 checklist 子项；通过的子项打 `[x]` 并追加审核证明。
- 审核不通过时，把具体问题打回同一个 subagent 修复。
- 全部必要子项通过后，更新结束时间和开发总结。
- 使用 `mygit-skill` 提交 subagent 的代码改动，排除 checklist 文件和 `summary/`。

subagent：

- 负责实际代码开发和必要测试。
- 不修改 checklist 文件。
- 不修改 `summary/` 目录。
- 不执行 git commit。
- 完成后汇报改动文件、实现摘要、测试命令和结果、风险或未完成项。

## 硬性规则

- checklist 路径必须由用户提供；不限定目录，但通常在 `summary/`。
- 用户提供提示词和 checklist 文件后，主 agent 必须先返回执行方案并等待明确确认。
- 用户确认前，不允许更新 checklist、启动 subagent、修改业务代码、运行测试或提交代码。
- 执行方案必须包含本轮目标、计划执行的阶段或子任务顺序、每个子任务的派发范围、预期验证方式、提交策略和已识别风险。
- 主 agent 可以更新 checklist 的开始时间、结束时间、开发总结、子项勾选状态和通过证明。
- 主 agent 不修改业务代码。
- 主 agent 不修改除 checklist 进度记录外的文档。
- subagent 不允许修改 checklist 文件或 `summary/`。
- 每个子任务必须开启新的 worker subagent；不要复用上一个子任务的 subagent。
- 任务必须串行执行；任何时候只允许一个 subagent 处于执行中。
- 不允许为多个阶段或子任务并行开启多个 subagent。
- subagent 运行期间，主 agent 必须耐心等待；不能因为等待时间长而停止 subagent 后自行修改。
- `wait_agent` 超时不等于失败；继续等待，或只做不依赖 subagent 结果的准备工作。
- 同一子任务审核不通过时，最多打回 subagent 修复 5 轮。
- 连续 5 轮仍不通过时停止流程，报告阻塞点、失败原因、已尝试轮次和当前工作区状态。
- 每次 git 提交必须使用 `mygit-skill` 流程，并明确排除 checklist 文件和 `summary/`。

## 执行流程

1. 读取 checklist 文件和用户提供的背景。
2. 检查 `git status --short`，确认是否存在无关改动；如有，隔离本轮范围。
3. 找到下一个未完成阶段或子任务。
4. 返回执行方案，至少包含：
   - 本轮目标和将处理的阶段或子任务。
   - 子任务执行顺序和每个子任务的 worker 派发范围。
   - 预计需要查看或修改的模块、目录或文件类型。
   - 每个子任务的验证方式或测试命令。
   - checklist 进度记录方式和 git 提交策略。
   - 已识别风险、阻塞点或需要用户决策的问题。
5. 停止流程，等待用户明确确认；未确认前不要继续执行。
6. 用户确认后，更新该阶段或子任务的开始时间，使用当前本地时间和时区。
7. 生成 subagent 任务说明。
8. spawn 一个新的 worker subagent；确认没有其他 subagent 正在执行。
9. 使用长等待轮次等待 subagent 完成；不要接手实现。
10. subagent 完成后检查改动范围，确认未修改 checklist 或 `summary/`。
11. 审核代码 diff，运行相关测试。
12. 按 checklist 子项逐项核对。
13. 对通过子项改为 `[x]`，并在同一行追加具体审核证明。
14. 若有必要子项不通过，整理失败原因、测试输出和期望行为，发回 subagent 修复。
15. 若阶段全部必要子项通过，更新结束时间和开发总结。
16. 使用 `mygit-skill` 提交代码改动，排除 checklist 文件和 `summary/`。
17. 关闭或停止使用当前已完成的 subagent。
18. 继续下一个未完成子任务，并为下一个子任务开启新的 subagent。

## Subagent 派发要求

派发给 subagent 的消息必须包含：

- checklist 文件路径。
- 当前阶段或子任务原文。
- 必要背景、相关设计、已有约束和验收目标。
- 允许修改的模块、目录或责任范围。
- 禁止修改 checklist 文件和 `summary/`。
- 禁止执行 git commit。
- 需要运行或建议运行的测试命令。
- 完成后必须汇报：
  - 修改文件列表
  - 实现摘要
  - 测试命令和结果
  - 未完成项、风险或需要主 agent 决策的问题

示例派发片段：

```text
你是 worker subagent，负责实现当前子任务。

Checklist: <path>
当前子任务: <原文>

限制：
- 不要修改 checklist 文件。
- 不要修改 summary/ 目录。
- 不要执行 git commit。
- 你不是代码库里唯一的执行者，不要回滚他人改动。

完成后汇报修改文件、实现摘要、测试结果和风险。
```

## 逐项审核和打勾

- 主 agent 审核时必须针对当前阶段或子任务下的每个 checklist 子项逐项确认。
- 不能只凭“阶段大体完成”就整段打勾。
- 每个子项必须基于代码位置、测试结果、运行结果、配置变更或明确行为证据判断。
- 只有确认通过的子项才能从 `[ ]` 改为 `[x]`。
- 将子项改为 `[x]` 时，必须在同一行追加审核通过证明。
- 证明必须具体，不能只写“已完成”或“已验证”。
- 已有证明的 `[x]` 子项应尽量保留；如果实现变化导致证明过期，再替换为新证据。
- 部分子项通过时可以先打勾并附证明，但阶段结束时间和总结必须等全部必要子项通过后再写。

证明格式示例：

```markdown
- [x] 拆分用户身份和角色字段。（验证：src/domain/user.rs:42 定义 identity_type，cargo test role_identity --lib 通过）
- [x] 登录响应返回新的身份字段。（验证：src/api/auth.rs:118 response 包含 identity_type）
- [x] 配置中包含默认角色映射。（验证：config/roles.toml 包含 guest/user/admin 映射）
```

## 审核失败处理

每轮失败反馈必须包含：

- 未通过的 checklist 子项。
- 观察到的问题。
- 相关文件、代码位置或测试输出。
- 期望的修复结果。
- 当前修复轮次，例如 `第 2/5 轮修复`。

不要自己修改代码修复失败项。将反馈发送给同一个 subagent，让 subagent 修复。

## 提交规则

- 提交前检查工作区，区分代码改动和 checklist 进度记录。
- checklist 文件和 `summary/` 默认不提交。
- 只暂存 subagent 为当前子任务产生且审核通过的代码、测试、配置或必要文档。
- 使用 `mygit-skill` 的提交流程和提交信息规范。
- 提交完成后确认工作区中只剩未提交的 checklist/summary 进度记录，或工作区干净。
