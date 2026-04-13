# claude-codex-Lightweight-skill

一个轻量的 Claude Code 斜杠命令 `/codex`，让你直接和 Codex 对话。自动续接已有线程（0 Claude token），或在首次使用时附带对话上下文开启新线程。

## 原理

```
你输入: /codex <消息>
              │
              ▼
      检测是否有活跃的 Codex 线程
              │
      ┌───────┴────────┐
    没有线程          有线程
      │                   │
      ▼                   ▼
  Claude 总结       检测上次 /codex 之后
  对话上下文        有没有新的 Claude 对话
  开新线程               │
                  ┌──────┴──────┐
               没有新对话      有新对话
               （连续调用）    （中间聊过）
                  │                │
                  ▼                ▼
            纯 resume        Claude 总结新对话
            直接续接         附上后再 resume
            0 Claude token   少量 Claude token
              │                │
              └──────┬─────────┘
                     ▼
              Codex 执行任务
              （读文件、写代码、自动保存）
                     │
                     ▼
              结果原样返回给你
```

**三种场景的 token 消耗：**

| 场景 | Claude token | 说明 |
|---|---|---|
| 连续 `/codex` | **0** | Codex 线程有记忆，直接续接 |
| 中间有 Claude 对话后再 `/codex` | 少量 | 只总结新增的对话部分 |
| 第一次 / `--fresh` | 少量 | 总结完整上下文开新线程 |

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

## 为什么不能完全共享上下文

Claude 和 Codex 是**两个独立进程**，进程之间天然隔离——即使都在本地运行，一个进程也无法直接读另一个进程的内存。Claude 的对话历史存在 Claude Code 进程里，Codex 启动时只能读到通过命令行传入的字符串。

所以"共享上下文"的唯一方式就是：Claude 把关键信息摘要成文字，通过 prompt 传给 Codex。这也是本 skill 做的事。

## License

MIT
