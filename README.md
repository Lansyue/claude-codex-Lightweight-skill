# claude-codex-Lightweight-skill

一个轻量的 Claude Code 斜杠命令 `/codex`，让你直接和 Codex 对话，并自动附带当前对话上下文。

## 它做什么

输入 `/codex <任何内容>` 时，Claude 会：
1. 从当前对话中提炼相关上下文
2. 将上下文拼在你的消息前面
3. 通过 Codex CLI 发送给 Codex
4. 原样返回 Codex 的输出

## 和 `/codex:rescue` 的区别

| 命令 | 上下文 | 定位 |
|---|---|---|
| `/codex`（本项目） | Claude 自动提炼对话上下文 | 通用，问问题/写代码/分析都行 |
| `/codex:rescue` | 原文转发，无上下文 | 专为调试/修复设计 |

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
