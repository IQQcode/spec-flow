# 可移植性说明

## 与 Comate mission-flow 的对应关系

本 skill 归纳自 Comate 内置 Spec 模式所依赖的 mission-flow 插件（`~/.comate/plugins/mission-flow/`）。原插件是 12 个独立 skill 加会话启动 hook，本 skill 把主链路压成一个文件加若干模板。

| mission-flow skill | 本 skill 对应位置 | 差异 |
| --- | --- | --- |
| `using-mission-flow` | 主流程三条硬规则 | 原来靠 hook 自动注入，这里靠 description 触发或用户显式调用 |
| `think` | 阶段 1 | 完整保留 |
| `design` | 阶段 2 + `doc-template.md` | 保留章节要求与文档风格，reviewer 派发改为可选 |
| `split` | 阶段 3 + `split-format.md` | Feature / Story 两层卡片改为单层工作项，编号 W1、W2，不绑定任何平台规范 |
| `plan` | 阶段 4 + `tasks-template.md` | 完整保留，含自审红线 |
| `subagent-impl` | 阶段 5a | 保留三段式派发与状态处理 |
| `direct-impl` | 阶段 5b | 完整保留 |
| `using-git-worktrees` / `finish-git-worktree` | 阶段 5 工作区安全 + 阶段 6 收尾 | 压缩为原则，不绑定固定 worktree 路径 |
| `ac-generator` | 阶段 4 的测试预期字段 | 降为可选增强 |
| `deepwiki` / `requesting-code-review` | 阶段 2 补充调研、阶段 5 review | 换成通用表述，不绑定内网工具 |

被刻意去掉的部分：iCafe 强依赖、Feature / Story 卡片层级规范、DeepWiki MCP、ICR 代码扫描、Comate hook 注入、固定的 worktree 目录约定。这些都是内网、公司流程或 IDE 侧的东西，在 Codex 等环境不可用，也不该由一个通用 skill 规定。

## 与 to-spec 的区别

`to-spec` 是一次性把当前对话综合成一份大 spec 发到 issue tracker，产物是给外部消费者看的需求合同，重点在穷举用户故事和确认测试接缝。

本 skill 是驱动一次完整研发过程的工作流，产物是 doc.md 加 tasks.md 加 summary.md，重点在阶段门禁和执行契约。两者不互相替代：想沉淀一份可交付的需求合同用 `to-spec`，想按流程把需求做完用本 skill。

## 各 Agent 落地差异

| 环境 | 结构化提问 | 子 agent | 任务跟踪 | 备注 |
| --- | --- | --- | --- | --- |
| Comate | question 工具 | 支持 | TodoWrite | 已有 mission-flow 时优先用原插件 |
| Claude Code | AskUserQuestion | Task 工具 | TodoWrite | 能力最接近原插件 |
| Codex | 无，用编号列表让用户回数字 | 通常无，主 agent 顺序执行阶段 5a | 无，用 tasks.md 的 checkbox 当进度 | 本 skill 的主要目标场景 |
| 其他 CLI Agent | 退化为编号列表 | 有则用，无则顺序执行 | 退化为 tasks.md checkbox | 阶段与门禁不变 |
没有子 agent 能力时，阶段 5a 退化为：主 agent 逐个 Task 实现，实现后自己按规格符合性与代码质量两个角度分别过一遍，两轮检查都要写出结论，不能合并成一句「没问题」。

## 安装方式

复制 `spec-flow/` 整个目录到目标环境的 skills 目录：

- Codex / 通用：`~/.agents/skills/`
- Claude Code：`~/.claude/skills/`
- Comate：`~/.comate/skills/`
- 只给某个项目用：项目内的 `.agents/skills/` 或 `.comate/skills/`

目录自包含，无脚本依赖，无需额外配置。
