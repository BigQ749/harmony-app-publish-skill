# 鸿蒙应用上架 Skill

给 AI 编程助手用的作业单：按国内华为 HarmonyOS NEXT 路径，从网页壳、包名、发布签名、商店文案、9:16 截图到 AppGallery Connect 提审。

适用于 Cursor、Codex、Claude Code 等能加载 Agent Skill 的工具。

## 装到本机

**Cursor**

把本仓库拷到：

```text
~/.cursor/skills/harmony-app-publish/
```

Windows 一般是：

```text
C:\Users\<你>\.cursor\skills\harmony-app-publish\
```

目录里至少要有 `SKILL.md`。

**Claude / Codex**

拷到对应的 skills 目录，或在对话里说「按鸿蒙上架 skill 做」。

## 里面有什么

| 文件 | 用途 |
|---|---|
| `SKILL.md` | 作业单和红线 |
| `reference.md` | 命令、隐私表、错误码 |
| `examples.md` | 想吃先停对照，不要抄业务文案 |

## 触发说法

「按鸿蒙上架 skill 做」「上华为应用市场」「打 HAP/APP」「992」「发布证书」

## 不要提交的

密钥、`.p12`、`.cer`、`.p7b`、密码、华为账号。这些只放在本机 `.sdks/harmony-sign/`。

## 许可

MIT
