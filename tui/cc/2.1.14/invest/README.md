# Claude Code v2.1.14 源码分析

> 基于 `@anthropic-ai/claude-code` v2.1.14 bundled 源码逆向分析

## 目录结构

```
invest/
├── README.md                 # 本文件 - 总索引
├── system-prompt.md          # 系统提示词分析
├── sub-agents.md             # Sub Agent 分析
├── tools.md                  # 内置工具分析
└── prompts/                  # Prompt Templates
    ├── README.md             # Prompts 索引
    │
    │ # 工具提示词
    ├── tool-bash.md          # Bash (含 Git commit/PR)
    ├── tool-read.md          # Read
    ├── tool-write.md         # Write
    ├── tool-edit.md          # Edit
    ├── tool-glob.md          # Glob
    ├── tool-grep.md          # Grep
    ├── tool-task.md          # Task
    ├── tool-webfetch.md      # WebFetch
    ├── tool-websearch.md     # WebSearch
    ├── tool-todowrite.md     # TodoWrite
    ├── tool-askuserquestion.md   # AskUserQuestion
    ├── tool-planmode.md      # EnterPlanMode/ExitPlanMode
    ├── tool-notebookedit.md  # NotebookEdit
    ├── tool-skill.md         # Skill
    ├── tool-taskoutput-killshell.md  # TaskOutput/KillShell
    │
    │ # Agent 提示词
    ├── agent-bash.md         # Bash Agent
    ├── agent-general-purpose.md  # General Purpose Agent
    ├── agent-explore.md      # Explore Agent
    ├── agent-plan.md         # Plan Agent
    ├── agent-statusline-setup.md # Statusline Setup Agent
    ├── agent-claude-code-guide.md # Claude Code Guide Agent
    │
    │ # 其他提示词
    ├── output-styles.md      # 输出风格
    └── specialized-prompts.md # 专用提示词
```

## 快速导航

### 核心文档

| 文档                                   | 说明                                       |
| -------------------------------------- | ------------------------------------------ |
| [system-prompt.md](./system-prompt.md) | 主系统提示词结构分析                       |
| [sub-agents.md](./sub-agents.md)       | 6 种内置 Sub Agent 详解                    |
| [tools.md](./tools.md)                 | 19 种内置工具详解                          |

### 系统提示词关键章节

| 章节                      | 说明                                           |
| ------------------------- | ---------------------------------------------- |
| Tone and Style            | 输出风格：简洁、无 emoji、Markdown 格式        |
| Professional Objectivity  | 专业客观：技术准确性优先，不过度奉承           |
| No Time Estimates         | 禁止时间估算：不预测任务时长                   |
| Task Management           | 任务管理：频繁使用 TodoWrite                   |
| Doing Tasks               | 任务执行：先读后改、避免过度工程化             |
| Tool Usage Policy         | 工具策略：专用工具优先于 bash                  |
| Git Operations            | Git 操作：安全协议、commit/PR 流程             |

### 内置 Agent

| Agent Type          | Model   | 用途                     | 只读   |
| ------------------- | ------- | ------------------------ | ------ |
| `Bash`              | inherit | 命令执行                 | ❌     |
| `general-purpose`   | inherit | 通用任务                 | ❌     |
| `Explore`           | haiku   | 代码库探索               | ✅     |
| `Plan`              | inherit | 实现计划设计             | ✅     |
| `statusline-setup`  | sonnet  | 状态栏配置               | ❌     |
| `claude-code-guide` | haiku   | 使用指南                 | ✅     |

### 内置工具分类

| 类别         | 工具                                               |
| ------------ | -------------------------------------------------- |
| 文件操作     | Read, Write, Edit, NotebookEdit                    |
| 代码搜索     | Glob, Grep                                         |
| 命令执行     | Bash, KillShell                                    |
| Web 访问     | WebFetch, WebSearch                                |
| 任务管理     | Task, TaskOutput, TodoWrite                        |
| 用户交互     | AskUserQuestion                                    |
| 规划模式     | EnterPlanMode, ExitPlanMode                        |
| 技能执行     | Skill                                              |
| MCP 集成     | ListMcpResources, ReadMcpResource, Mcp             |
| 配置管理     | Config                                             |

## 关键发现

### 1. 系统提示词动态组合

系统提示词由多个模块化部分根据上下文动态组合：
- 可用工具集
- 用户订阅类型
- 后台任务开关
- MCP 工具可用性
- 规划模式状态

### 2. 安全限制

```
IMPORTANT: Assist with authorized security testing, defensive security, CTF challenges,
and educational contexts. Refuse requests for destructive techniques, DoS attacks,
mass targeting, supply chain compromise, or detection evasion for malicious purposes.
```

### 3. 工具使用策略

专用工具优先于 bash 命令：

| 操作         | 应该使用     | 不应该使用     |
| ------------ | ------------ | -------------- |
| 读取文件     | Read         | cat/head/tail  |
| 编辑文件     | Edit         | sed/awk        |
| 创建文件     | Write        | echo/cat <<EOF |
| 文件搜索     | Glob         | find/ls        |
| 内容搜索     | Grep         | grep/rg        |

### 4. Git 安全协议

- 永远不要更新 git config
- 永远不要运行 `push --force`、`hard reset`
- 永远不要跳过 hooks
- 永远创建新提交，不用 `--amend`
- 不主动提交，除非用户明确要求

### 5. 代码库探索

当探索代码库时，优先使用 `Task` 工具配合 `subagent_type=Explore`：
- 使用 haiku 模型（更快更便宜）
- 只读限制（不能编辑/写入文件）
- 支持指定彻底程度：quick / medium / very thorough

## 源码位置

```
/Users/wanchenfang/.local/state/fnm_multishells/*/lib/node_modules/@anthropic-ai/claude-code/
├── cli.js              # 11 MB bundled 可执行文件
├── sdk-tools.d.ts      # TypeScript 类型定义
├── package.json        # 包配置
└── vendor/ripgrep/     # ripgrep 二进制
```
