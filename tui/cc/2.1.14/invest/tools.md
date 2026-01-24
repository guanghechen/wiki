# Claude Code Built-in Tools

> Source: `@anthropic-ai/claude-code` v2.1.14

## Overview

Claude Code 提供了一组内置工具，分为以下几类：

| 类别         | 工具                                            |
| ------------ | ----------------------------------------------- |
| 文件操作     | Read, Write, Edit, NotebookEdit                 |
| 代码搜索     | Glob, Grep                                      |
| 命令执行     | Bash, KillShell                                 |
| Web 访问     | WebFetch, WebSearch                             |
| 任务管理     | Task, TaskOutput, TodoWrite                     |
| 用户交互     | AskUserQuestion, Skill                          |
| 规划模式     | EnterPlanMode, ExitPlanMode                     |
| MCP 集成     | ListMcpResources, ReadMcpResource, Mcp          |
| 配置         | Config                                          |

---

## 1. Bash

执行 bash 命令，支持超时控制。

See [tool-bash.txt](prompts/tool-bash.txt) / [tool-bash-git.txt](prompts/tool-bash-git.txt)

```typescript
interface IBashInput {
  command: string;                      // 要执行的命令
  timeout?: number;                     // 超时时间 (最大 600000ms = 10分钟)
  description?: string;                 // 命令描述
  run_in_background?: boolean;          // 后台运行
  dangerouslyDisableSandbox?: boolean;  // 禁用沙盒模式
}
```

**特点**：

- 工作目录在命令间持久化
- Shell 状态（其他所有内容）不持久化
- Shell 环境从用户 profile (bash/zsh) 初始化

**描述示例**：

- 简单命令：`"List files in current directory"`
- 复杂命令：`"Find and delete all .tmp files recursively"`

**限制**：

- 避免使用交互式命令（如 `git rebase -i`）
- 不应用于文件操作（读/写/编辑/搜索）
- 使用专用工具替代 `find`、`grep`、`cat` 等

---

## 2. Read (FileRead)

读取本地文件系统中的文件。

See [tool-read.txt](prompts/tool-read.txt)

```typescript
interface IReadInput {
  file_path: string;   // 绝对路径
  offset?: number;     // 起始行号
  limit?: number;      // 读取行数
}
```

**特点**：

- 默认从文件开头读取最多 2000 行
- 超过 2000 字符的行会被截断
- 结果使用 `cat -n` 格式，行号从 1 开始
- 支持读取图片（PNG、JPG 等）
- 支持读取 PDF 文件
- 支持读取 Jupyter notebooks (.ipynb)
- 只能读取文件，不能读取目录

---

## 3. Write (FileWrite)

写入文件到本地文件系统。

See [tool-write.txt](prompts/tool-write.txt)

```typescript
interface IWriteInput {
  file_path: string;   // 绝对路径
  content: string;     // 文件内容
}
```

**限制**：

- 如果文件存在，必须先使用 Read 工具读取
- 优先编辑现有文件，避免创建新文件
- 不要主动创建文档文件 (*.md) 或 README
- 不使用 emoji 除非用户明确要求

---

## 4. Edit (FileEdit)

在文件中执行精确的字符串替换。

See [tool-edit.txt](prompts/tool-edit.txt)

```typescript
interface IEditInput {
  file_path: string;     // 绝对路径
  old_string: string;    // 要替换的文本
  new_string: string;    // 替换后的文本
  replace_all?: boolean; // 替换所有出现（默认 false）
}
```

**特点**：

- 必须在会话中至少使用过一次 Read 工具后才能编辑
- 保持精确的缩进（tabs/spaces）
- 如果 `old_string` 不唯一，编辑会失败
- 使用 `replace_all` 重命名变量

---

## 5. Glob

快速文件模式匹配工具。

See [tool-glob.txt](prompts/tool-glob.txt)

```typescript
interface IGlobInput {
  pattern: string;  // glob 模式，如 "**/*.js"
  path?: string;    // 搜索目录（默认当前目录）
}
```

**特点**：

- 适用于任何大小的代码库
- 支持 glob 模式如 `"**/*.js"` 或 `"src/**/*.ts"`
- 结果按修改时间排序

---

## 6. Grep

基于 ripgrep 的强大搜索工具。

See [tool-grep.txt](prompts/tool-grep.txt)

```typescript
interface IGrepInput {
  pattern: string;                                              // 正则表达式模式
  path?: string;                                                // 搜索路径
  glob?: string;                                                // 文件过滤 glob
  output_mode?: "content" | "files_with_matches" | "count";     // 默认 files_with_matches
  "-B"?: number;                                                // 匹配前的行数
  "-A"?: number;                                                // 匹配后的行数
  "-C"?: number;                                                // 上下文行数
  "-n"?: boolean;                                               // 显示行号
  "-i"?: boolean;                                               // 忽略大小写
  type?: string;                                                // 文件类型 (js, py, rust, go, java 等)
  head_limit?: number;                                          // 限制输出
  offset?: number;                                              // 跳过前 N 行
  multiline?: boolean;                                          // 多行模式
}
```

**输出模式**：

- `files_with_matches`（默认）：只显示文件路径
- `content`：显示匹配行
- `count`：显示匹配计数

**注意**：

- 使用 ripgrep 语法（非 grep）
- 花括号需要转义：使用 `interface\{\}` 查找 `interface{}`

---

## 7. Task

启动专门代理处理复杂任务。详见 [sub-agents.md](./sub-agents.md)

```typescript
interface ITaskInput {
  description: string;                     // 3-5 词的短描述
  prompt: string;                          // 任务描述
  subagent_type: string;                   // 代理类型
  model?: "sonnet" | "opus" | "haiku";
  resume?: string;                         // 恢复代理 ID
  run_in_background?: boolean;
  max_turns?: number;
}
```

---

## 8. TaskOutput

获取运行中或已完成任务的输出。

See [tool-taskoutput.txt](prompts/tool-taskoutput.txt)

```typescript
interface ITaskOutputInput {
  task_id: string;    // 任务 ID
  block: boolean;     // 是否等待完成
  timeout: number;    // 最大等待时间 (ms)
}
```

**用途**：

- 后台 shell
- 异步代理
- 远程会话

---

## 9. KillShell

终止运行中的后台 bash shell。

See [tool-killshell.txt](prompts/tool-killshell.txt)

```typescript
interface IKillShellInput {
  shell_id: string;  // Shell ID
}
```

---

## 10. WebFetch

获取 URL 内容并使用 AI 模型处理。

See [tool-webfetch.txt](prompts/tool-webfetch.txt)

```typescript
interface IWebFetchInput {
  url: string;     // 要获取的 URL
  prompt: string;  // 处理内容的提示
}
```

**特点**：

- 获取 URL 内容，将 HTML 转换为 Markdown
- 使用小型快速模型处理内容
- HTTP URL 自动升级为 HTTPS
- 包含 15 分钟自清理缓存
- 重定向到不同主机时会通知并提供重定向 URL

---

## 11. WebSearch

搜索网络并返回结果。

See [tool-websearch.txt](prompts/tool-websearch.txt)

```typescript
interface IWebSearchInput {
  query: string;              // 搜索查询
  allowed_domains?: string[]; // 只包含这些域名
  blocked_domains?: string[]; // 排除这些域名
}
```

**要求**：

- 回答后必须包含 "Sources:" 部分
- 列出所有相关 URL 作为 Markdown 超链接
- 只在美国可用

---

## 12. TodoWrite

创建和管理结构化任务列表。

See [tool-todowrite.txt](prompts/tool-todowrite.txt)

```typescript
interface ITodoWriteInput {
  todos: {
    content: string;                                    // 任务内容（祈使句）
    status: "pending" | "in_progress" | "completed";
    activeForm: string;                                 // 进行时形式
  }[];
}
```

**何时使用**：

- 复杂多步骤任务（3+ 步骤）
- 用户明确要求
- 用户提供多个任务
- 收到新指令后
- 开始任务前标记为 `in_progress`
- 完成任务后标记为 `completed`

**何时不使用**：

- 单一简单任务
- 琐碎任务
- 少于 3 个简单步骤的任务
- 纯对话或信息性任务

**任务状态**：

- `pending`：尚未开始
- `in_progress`：当前正在处理（同时只能有一个）
- `completed`：成功完成

---

## 13. AskUserQuestion

在执行过程中询问用户问题。

See [tool-askuserquestion.txt](prompts/tool-askuserquestion.txt)

```typescript
interface IAskUserQuestionInput {
  questions: {
    question: string;      // 完整问题
    header: string;        // 短标签 (最多 12 字符)
    options: {
      label: string;       // 选项显示文本
      description: string; // 选项说明
    }[];                   // 2-4 个选项
    multiSelect: boolean;  // 是否多选
  }[];                     // 1-4 个问题
  answers?: Record<string, string>;
  metadata?: { source?: string };
}
```

**用途**：

- 收集用户偏好或需求
- 澄清模糊指令
- 获取实现选择的决定
- 向用户提供选项

**注意**：

- 用户总是可以选择 "Other" 提供自定义输入
- 不需要包含 "Other" 选项，系统自动提供

---

## 14. EnterPlanMode

开始规划模式以设计实现方案。

See [tool-enterplanmode.txt](prompts/tool-enterplanmode.txt)

```typescript
interface IEnterPlanModeInput {
  // 无必需参数
}
```

**何时使用**：

- 新功能实现
- 多种有效方案
- 代码修改
- 架构决策
- 多文件更改
- 需求不明确
- 用户偏好重要

**何时不使用**：

- 单行或少量行修复
- 用户给出非常具体的指令
- 纯研究/探索任务

---

## 15. ExitPlanMode

完成规划后退出规划模式。

See [tool-exitplanmode.txt](prompts/tool-exitplanmode.txt)

```typescript
interface IExitPlanModeInput {
  allowedPrompts?: {
    tool: "Bash";
    prompt: string;  // 语义描述如 "run tests"
  }[];
  pushToRemote?: boolean;
  remoteSessionId?: string;
  remoteSessionUrl?: string;
  remoteSessionTitle?: string;
}
```

**何时使用**：

- 计划已完成且明确
- 任务需要规划代码实现步骤
- 不用于研究任务

---

## 16. Skill

在主对话中执行技能。

See [tool-skill.txt](prompts/tool-skill.txt)

```typescript
interface ISkillInput {
  skill: string;    // 技能名称
  args?: string;    // 可选参数
}
```

**用途**：

- 用户使用 `/<skill-name>` 调用技能
- 例如 `/commit`、`/review-pr`

---

## 17. NotebookEdit

编辑 Jupyter Notebook 单元格。

See [tool-notebookedit.txt](prompts/tool-notebookedit.txt)

```typescript
interface INotebookEditInput {
  notebook_path: string;                          // 绝对路径
  cell_id?: string;                               // 单元格 ID
  new_source: string;                             // 新内容
  cell_type?: "code" | "markdown";
  edit_mode?: "replace" | "insert" | "delete";
}
```

---

## 18. MCP Tools

### ListMcpResources

列出 MCP 资源。

```typescript
interface IListMcpResourcesInput {
  server?: string;  // 可选服务器名称过滤
}
```

### ReadMcpResource

读取 MCP 资源。

```typescript
interface IReadMcpResourceInput {
  server: string;  // MCP 服务器名称
  uri: string;     // 资源 URI
}
```

### Mcp

通用 MCP 调用。

```typescript
interface IMcpInput {
  [k: string]: unknown;
}
```

---

## 19. Config

读取或设置配置。

```typescript
interface IConfigInput {
  setting: string;                      // 设置键
  value?: string | boolean | number;    // 新值（省略则获取当前值）
}
```

支持深层键如 `"permissions.defaultMode"`。

---

## Best Practices

### 并行调用

- 独立的工具调用应并行执行
- 有依赖的工具调用必须顺序执行
- 不要对依赖的调用使用占位符或猜测参数

### 专用工具优先

| 操作       | 推荐工具       | 避免使用       |
| ---------- | -------------- | -------------- |
| 文件读取   | Read           | cat/head/tail  |
| 文件编辑   | Edit           | sed/awk        |
| 文件写入   | Write          | echo/cat <<EOF |
| 文件搜索   | Glob           | find/ls        |
| 内容搜索   | Grep           | grep/rg (bash) |
| 代码库探索 | Task (Explore) | 直接搜索命令   |

### 探索代码库

当探索代码库以收集上下文或回答非针对性问题时，使用 `Task` 工具配合 `subagent_type=Explore`：

```
user: Where are errors from the client handled?
assistant: [使用 Task 工具，subagent_type=Explore]
```
