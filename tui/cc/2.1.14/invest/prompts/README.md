# Claude Code v2.1.14 Prompt Templates

从 Claude Code v2.1.14 bundled 代码中提取的所有 prompt template。

## 目录结构

```
prompts/
├── README.md                          # 本文件
├── tool-*.txt                         # 工具 prompts (18)
├── agent-*.txt                        # Agent prompts (6)
└── misc-*.txt                         # 其他 prompts (18)
```

## 文件格式

每个文件统一格式：
1. 标题 - `# Tool/Agent/Misc: 名称`
2. 作用 - 简要描述
3. 使用场景 - 列出典型使用场景
4. 关键点 - 重要配置和注意事项
5. 分隔线 - 100 个 `=` 字符
6. 原始 Prompt - 完整的 template 内容

---

## Tool Prompts (18)

核心工具的 description/prompt，用于告诉 Claude 如何使用每个工具。

| 文件                         | 工具名            | 说明                                         |
|------------------------------|-------------------|----------------------------------------------|
| `tool-read.txt`              | Read              | 文件读取（支持文本、图片、PDF、Notebook）    |
| `tool-write.txt`             | Write             | 文件写入                                     |
| `tool-edit.txt`              | Edit              | 文件编辑（精确字符串替换）                   |
| `tool-glob.txt`              | Glob              | 文件模式匹配                                 |
| `tool-grep.txt`              | Grep              | 内容搜索（基于 ripgrep）                     |
| `tool-bash.txt`              | Bash              | 命令执行                                     |
| `tool-bash-git.txt`          | Bash (Git)        | Git 操作指南（commit、PR）                   |
| `tool-task.txt`              | Task              | 启动子 agent                                 |
| `tool-taskoutput.txt`        | TaskOutput        | 获取任务输出                                 |
| `tool-killshell.txt`         | KillShell         | 终止后台 shell                               |
| `tool-todowrite.txt`         | TodoWrite         | 任务管理                                     |
| `tool-askuserquestion.txt`   | AskUserQuestion   | 向用户提问                                   |
| `tool-webfetch.txt`          | WebFetch          | 获取网页内容                                 |
| `tool-websearch.txt`         | WebSearch         | 网络搜索                                     |
| `tool-enterplanmode.txt`     | EnterPlanMode     | 进入计划模式                                 |
| `tool-exitplanmode.txt`      | ExitPlanMode      | 退出计划模式                                 |
| `tool-notebookedit.txt`      | NotebookEdit      | Jupyter Notebook 编辑                        |
| `tool-skill.txt`             | Skill             | 执行技能/slash command                       |

---

## Agent Prompts (6)

子 agent 的系统提示，定义每种 agent 的行为和能力。

| 文件                           | Agent 类型         | 说明                                        |
|--------------------------------|--------------------|---------------------------------------------|
| `agent-bash.txt`               | Bash               | 命令执行专家                                |
| `agent-general-purpose.txt`    | general-purpose    | 通用目的 agent                              |
| `agent-explore.txt`            | Explore            | 代码库探索专家（Haiku 模型，只读）          |
| `agent-plan.txt`               | Plan               | 软件架构师（只读）                          |
| `agent-claude-code-guide.txt`  | claude-code-guide  | Claude Code/SDK/API 指南                    |
| `agent-statusline-setup.txt`   | statusline-setup   | 状态栏设置                                  |

---

## Misc Prompts (18)

系统提示片段、指南、提醒等辅助性 prompt。

### 会话管理

| 文件                              | 说明                                           |
|-----------------------------------|------------------------------------------------|
| `misc-topic-detection.txt`        | 话题检测 - 检测新对话主题并生成标题            |
| `misc-session-title-generator.txt`| 会话标题生成器 - 生成标题和 git 分支名         |
| `misc-conversation-summary.txt`   | 对话摘要 - 增量式对话压缩                      |
| `misc-auto-compact-summary.txt`   | 自动压缩摘要 - context 超限时的详细摘要        |
| `misc-session-memory.txt`         | Session Memory - MEMORY.md 模板                |

### 计划模式

| 文件                              | 说明                                           |
|-----------------------------------|------------------------------------------------|
| `misc-plan-mode-instructions.txt` | Plan Mode 完整指令 - 5 阶段工作流              |

### 行为指南

| 文件                              | 说明                                           |
|-----------------------------------|------------------------------------------------|
| `misc-professional-objectivity.txt` | 专业客观性 - 优先技术准确性                  |
| `misc-no-time-estimates.txt`      | 禁止时间估算                                   |
| `misc-avoid-over-engineering.txt` | 避免过度工程化                                 |

### 安全相关

| 文件                              | 说明                                           |
|-----------------------------------|------------------------------------------------|
| `misc-security-testing-guidelines.txt` | 安全测试指南                              |
| `misc-malware-analysis-reminder.txt`   | 恶意代码分析提醒                          |

### 功能扩展

| 文件                              | 说明                                           |
|-----------------------------------|------------------------------------------------|
| `misc-chrome-browser-automation.txt` | Chrome 浏览器自动化指令                     |
| `misc-agent-architect.txt`        | Agent 生成器 - 根据描述创建 agent 配置         |

### 分析工具

| 文件                              | 说明                                           |
|-----------------------------------|------------------------------------------------|
| `misc-git-history-analysis.txt`   | Git 历史分析 - 识别核心文件                    |
| `misc-user-message-analysis.txt`  | 用户消息分析 - 检测交互特征                    |

### 系统提醒

| 文件                              | 说明                                           |
|-----------------------------------|------------------------------------------------|
| `misc-todowrite-reminder.txt`     | TodoWrite 使用提醒                             |
| `misc-claude-background-info.txt` | Claude 模型背景信息                            |
| `misc-web-search-assistant.txt`   | Web 搜索助手提示                               |

---

## 统计

| 分类            | 数量 |
|-----------------|------|
| Tool Prompts    |   18 |
| Agent Prompts   |    6 |
| Misc Prompts    |   18 |
| **总计**        |   42 |

---

## 注意事项

1. 这些 prompt 是从 minified/bundled 的 JavaScript 代码中提取的，可能有细微格式差异
2. 部分 prompt 包含动态变量（如 `{{CURRENT_DATE}}`、`{{AVAILABLE_SKILLS_LIST}}`）
3. 某些 prompt 可能根据用户设置或环境动态调整
4. 这是 v2.1.14 版本的快照，后续版本可能有变化
