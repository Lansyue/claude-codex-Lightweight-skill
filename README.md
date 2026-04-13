# claude-codex-Lightweight-skill

一个轻量的 Claude Code 斜杠命令 `/codex`，让你直接和 Codex 对话，并自动附带当前对话上下文。

## 它做什么

输入 `/codex <任何内容>` 时，Claude 会：
1. 从当前对话中提炼相关上下文
2. 将上下文拼在你的消息前面
3. 通过 Codex CLI 发送给 Codex
4. 原样返回 Codex 的输出

## 原理

```
你输入: /codex 帮我优化这个函数
         │
         ▼
    Claude（对话中）
    ├── 读取当前对话历史
    ├── 提炼相关上下文（你在做什么、用什么技术栈、之前的决策）
    └── 组装 prompt:
            <context>
            [2-5行上下文摘要]
            </context>
            <task>
            帮我优化这个函数
            </task>
         │
         ▼
    codex --approval-mode full-auto "<prompt>"
         │
         ▼
    Codex CLI（独立进程）
    ├── 读取项目文件
    ├── 执行任务
    └── 自动保存修改
         │
         ▼
    结果原样返回给你
```

**为什么要传上下文？**

Codex 是独立运行的进程，默认不知道你和 Claude 聊了什么。如果你说"继续"或"按我们刚才讨论的改"，没有上下文的 Codex 完全不知道指的是什么。Claude 在中间做了一层翻译，把对话里的关键信息整理后传给 Codex，让它真正理解你的意图。



## 前置条件

1. **Claude Code** 已安装 — [claude.ai/code](https://claude.ai/code)
2. **Codex CLI** 已安装：
   ```bash
   npm install -g @openai/codex
   ```
3. **Codex 已登录**：
   ```bash
   codex login
   ```

## 安装

将 `commands/codex.md` 复制到你的 Claude 命令目录：

**macOS / Linux：**
```bash
cp commands/codex.md ~/.claude/commands/codex.md
```

**Windows：**
```powershell
cp commands\codex.md $env:USERPROFILE\.claude\commands\codex.md
```

重启 Claude Code，`/codex` 就会出现在命令列表里。

## 使用方法

```
/codex 帮我看一下这个函数有没有性能问题
/codex 给 UserService 加一个 findByEmail 方法
/codex 这个报错是什么意思？
/codex 用 async/await 重构 auth 模块
```

## License

MIT
