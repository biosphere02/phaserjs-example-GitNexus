# Phaser Examples GitNexus 代码知识图谱 — 环境搭建指南

## 概述

本目录存放 [Phaser 3 官方 Examples 仓库](https://github.com/phaserjs/examples) 的代码副本，用于通过 [GitNexus](https://github.com/nicepkg/gitnexus) 构建代码知识图谱，为 项目中的 LLM 提供精准的 Phaser API 参考源码。

---

## 目录结构

```
phaserjs-example-GitNexus/    ← 当前目录
   ├── src/                     ← Phaser 官方示例源码（2400+ 个 .js 文件）
   │    ├── physics/            ← 物理引擎示例（arcade/matter）
   │    ├── game objects/       ← 游戏对象示例
   │    ├── tweens/             ← 动画补间示例
   │    ├── fx/                 ← FX 特效管线示例
   │    └── ...                 ← 更多分类
   ├── assets/                  ← 示例用到的美术资源
   ├── SETUP_GUIDE.md           ← 本文件
   └── .gitnexus/               ← GitNexus 索引数据（analyze 后自动生成）
```

---

## 第一步：安装 GitNexus CLI

两种方式任选：

```bash
# 方式 A：全局安装（推荐，免去每次 npx 下载）
npm install -g gitnexus

# 方式 B：零配置使用（无需全局安装）
npx gitnexus
```

---

## 第二步：拉取 Phaser Examples 代码

如果本目录下还没有代码，请从 Phaser 官方仓库拉取：

```bash

# Clone Phaser 官方 Examples 仓库
git clone https://github.com/phaserjs/examples.git phaserjs-example-GitNexus
```

> **注意**: 仓库较大（约 700MB，含大量资源文件），请耐心等待。

---

## 第三步：构建 GitNexus 索引

进入代码目录，执行索引命令：

```bash
cd phaserjs-example-GitNexus

# 构建知识图谱索引（首次约 8 秒）
npx gitnexus analyze

# 如需强制全量重建索引
npx gitnexus analyze --force
```

索引完成后，会自动在当前目录生成 `.gitnexus/` 数据目录。

**验证索引状态**：

```bash
# 查看所有已索引仓库
npx gitnexus list

# 查看当前仓库状态
npx gitnexus status
```

预期输出类似：
```
phaserjs-example-GitNexus
  Path:    phaserjs-example-GitNexus
  Stats:   3217 files, 13834 symbols, 20225 edges
  Clusters:   326
  Processes:  192
  Status: ✅ up-to-date
```

---

## 第四步：项目集成方式

### MCP 是如何工作的

通过 Vite 中间件在开发服务器中集成了 GitNexus MCP 客户端：

```
LLM 决定调用 search_phaser_examples 工具
  → 前端 fetch('/api/mcp-query')
  → Vite 中间件（vite.config.ts）转发给本地 MCP server
  → npx gitnexus mcp（stdio 模式运行的 MCP server）
  → 搜索所有已索引仓库的知识图谱
  → 返回代码片段给 LLM 参考
```

### 关键文件

| 文件 | 作用 |
|------|------|
| `app/vite.config.ts` | MCP 客户端连接配置（`getMcpClient()` 函数） |
| `app/src/services/ai/prompts/mcp/gitnexus.ts` | LLM 工具定义 + 前端执行逻辑 |
| `app/src/services/ai/CompletionRunner.ts` | LLM 工具调用处理器 |

### 无需额外配置

索引建好后，项目即可自动使用。`npx gitnexus mcp` 会自动服务 **所有** 已在本机索引过的仓库。只需确保：

1. ✅ 已在 `phaserjs-example-GitNexus` 目录下执行过 `npx gitnexus analyze`
2. ✅ 启动 开发服务器时网络正常（MCP 通过 npx 启动需要网络）

---

## 常用命令速查

| 命令 | 说明 |
|------|------|
| `npx gitnexus analyze` | 构建/更新索引 |
| `npx gitnexus analyze --force` | 强制全量重建 |
| `npx gitnexus list` | 查看所有已索引仓库 |
| `npx gitnexus status` | 当前仓库索引状态 |
| `npx gitnexus clean` | 删除当前仓库索引 |
| `npx gitnexus mcp` | 启动 MCP 服务器（stdio 模式） |
| `npx gitnexus serve` | 启动 HTTP 服务器（供 Web UI 使用） |

---

## 故障排查

### Q1: `npx gitnexus mcp` 启动但查询返回空结果
- 确认已在 Phaser 目录执行过 `npx gitnexus analyze`
- 执行 `npx gitnexus list` 确认 `phaserjs-example-GitNexus` 在列表中

### Q2: Vite 开发服务器启动时报 MCP 连接错误
- 确认 Node.js 版本 >= 18
- 确认网络通畅（首次运行 `npx gitnexus@latest mcp` 需下载包）
- 可以先手动 `npm install -g gitnexus` 然后改 `vite.config.ts` 中的 command 为 `gitnexus`（跳过 npx 下载）

### Q3: 如何清理旧的无用索引
```bash
# 进入旧仓库目录执行 clean
cd <旧仓库路径>
npx gitnexus clean
```

### Q4: 索引数据存在哪里？
GitNexus 使用 KuzuDB 作为图数据库，索引数据存储在：
- Windows: `%LOCALAPPDATA%\gitnexus\` 或用户目录下 `.gitnexus/`
- Linux/Mac: `~/.local/share/gitnexus/`

---

## 参考链接

- [GitNexus GitHub 仓库](https://github.com/nicepkg/gitnexus)
- [GitNexus 社区教程](https://deepseek.club/t/topic/759)
- [Phaser 3 Examples 官方仓库](https://github.com/phaserjs/examples)
- [Phaser 3 API 文档](https://newdocs.phaser.io/docs/latest)
