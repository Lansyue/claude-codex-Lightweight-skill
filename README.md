# claude-codex-Lightweight-skill

一个轻量的 Claude Code 斜杠命令 `/codex`，让你直接和 Codex 对话。自动续接已有线程（0 Claude token），或在首次使用时附带对话上下文开启新线程。

## 原理

```
你输入: /codex 帮我优化这个函数
         │
         ▼
    检测是否有活跃的 Codex 线程
         │
    ┌────┴────┐
   有线程    没有线程（第一次）
    │              │
    ▼              ▼
 --resume-last   Claude 提炼对话上下文
 直接续接        组装 prompt 开新线程
 0 Claude token  少量 Claude token
    │              │
    └────┬─────────┘
         ▼
    Codex CLI 执行任务
    （读文件、写代码、自动保存）
         │
         ▼
    结果原样返回给你
```

**为什么续接线程能省 token？**

Codex 线程有自己的记忆，知道之前做了什么、说了什么。续接时 Claude 不需要重新总结上下文，直接把你的新消息转发给 Codex 即可，Claude token 消耗为 0。

## 前置条件

1. **Claude Code** 已安装 — [claude.ai/code](https://claude.ai/code)
2. **codex-plugin-cc** 已安装 — [github.com/openai/codex-plugin-cc](https://github.com/openai/codex-plugin-cc)
3. **Codex CLI** 已安装：
   ```bash
   npm install -g @openai/codex
   ```
4. **Codex 已登录**：
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

```bash
# 普通使用（自动检测是否续接）
/codex 帮我看一下这个函数有没有性能问题
/codex 给 UserService 加一个 findByEmail 方法
/codex 这个报错是什么意思？

# 强制开新线程（Claude 重新总结上下文）
/codex --fresh 从头开始，帮我重构 auth 模块
```

## Token 消耗

| 情况 | Claude token | 说明 |
|---|---|---|
| 续接已有线程 | 0 | Codex 自己有记忆 |
| 第一次 / 无线程 | 少量 | Claude 提炼上下文 |
| `--fresh` 强制新开 | 少量 | Claude 重新总结 |

## License

MIT
