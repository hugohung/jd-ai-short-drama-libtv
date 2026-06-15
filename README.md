# 🎬 LibTV 脚本及分镜生成助手

> **WorkBuddy Skill** — 将京东AI短剧剧本助手产出的剧本文档上传至 LibTV 画布，自动完成项目创建、节点布局、脚本生成和分镜图准备

[![Version](https://img.shields.io/badge/version-1.1.0-blue.svg)](https://github.com/hugohung/workbuddy-skill-jd-ai-short-drama-libtv/releases)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![WorkBuddy](https://img.shields.io/badge/WorkBuddy-skill-orange.svg)](https://www.codebuddy.cn)

---

## 📊 核心能力

本 Skill 是京东AI短剧创作流程的**出片环节**，自动将剧本助手生成的剧本文档导入 LibTV 画布，并完成脚本生成和分镜图准备。

### ✨ 主要功能

- 📋 **自动解析剧本文档** — 提取剧本正文、人物设定、场景设定、封面/开场提示词
- 🎨 **创建 LibTV 项目** — 自动在 LibTV 中创建新项目
- 📍 **动态节点布局** — 左右两列动态布局（用户产出左列 / AI生成右列），节点数随上游产出动态变化
- 📦 **横竖版本拆分** — 静态封面提示词自动拆为横版+竖版独立节点
- 🤖 **自动生成脚本** — 触发 LibTV 脚本生成，创建分镜行
- 🖼️ **自动生成分镜图** — 使用 Lib Navo 2 模型生成分镜图（16:9）
- 🚫 **安全保护** — 严禁触发视频生成操作，避免高额费用

---

## 🎯 适用场景

- 🎬 **京东AI短剧出片** — 将剧本助手生成的剧本导入 LibTV
- 🤖 **自动化画布搭建** — 快速完成 LibTV 画布节点布局
- 🎨 **分镜图批量生成** — 自动生成所有分镜的预览图
- 🔄 **衔接创作流程** — 连接剧本创作和最终出片环节

---

## 🚀 快速开始

### 前置要求

1. **剧本助手产出** — 用户必须已通过「京东AI短剧剧本助手」生成完整剧本文档（Markdown 格式）
2. **LibTV CLI** — 已安装 `libtv` 命令行工具（版本 ≥ 1.0.2）
3. **LibTV 登录** — 用户必须已完成 `libtv login web` 或 `libtv login phone` 登录
4. **libtv-cli skill** — 已安装 libtv-cli skill（`~/.workbuddy/skills/libtv-cli/`）

### 安装 LibTV CLI

下载 LibTV CLI Skill 安装包：

```
https://liblibai-web-static.liblib.cloud/cli/1.0.2/libtv-cli-skill.zip
```

解压后参照 `scripts/install.md` 完成安装。安装路径（默认）：
- **Windows**: `%USERPROFILE%\.libtv\libtv.exe`
- **macOS / Linux**: `~/.libtv/libtv`

### 使用示例

在 WorkBuddy 对话中直接说：

```
进入libtv出片助手
```

```
将剧本上传到libtv
```

```
生成分镜
```

---

## 📋 工作流程

```
┌─────────────────────────────────────────────────────┐
│  📋 第1步：解析剧本文档                           │
│  - 读取剧本文档（Markdown 格式）                  │
│  - 提取剧本正文、人物设定、场景设定               │
│  - 提取静态封面提示词（横版+竖版分开）           │
│  - 提取视频开场提示词（按需1-2个）               │
│  - 构建 script 节点 prompt                        │
└─────────────────┬───────────────────────────────────┘
                  ▼
┌─────────────────────────────────────────────────────┐
│  📋 第2步：创建 LibTV 项目                       │
│  - 使用剧名作为项目名称                           │
│  - 执行 libtv project create                      │
│  - 绑定项目到当前目录                            │
└─────────────────┬───────────────────────────────────┘
                  ▼
┌─────────────────────────────────────────────────────┐
│  📋 第3步：创建用户产出节点（左列，动态数量）    │
│  - y = 100 + index × 350                         │
│  - 最少6个，最多7个节点                          │
│  - 剧本正文 / 人物设定 / 场景设定                │
│  - 封面提示词横版+竖版 / 开场提示词（按需）      │
└─────────────────┬───────────────────────────────────┘
                  ▼
┌─────────────────────────────────────────────────────┐
│  🤖 第4步：创建AI生成节点（右列）                │
│  - 剧本 script 节点 (950, 100)                   │
└─────────────────┬───────────────────────────────────┘
                  ▼
┌─────────────────────────────────────────────────────┐
│  🤖 第5步：运行脚本生成                         │
│  - 触发 script 节点生成分镜行                    │
│  - 等待 CLI 返回结果（10-30秒）                  │
└─────────────────┬───────────────────────────────────┘
                  ▼
┌─────────────────────────────────────────────────────┐
│  🤖 第6步：生成分镜图                           │
│  - 使用 libtv script storyboard 命令              │
│  - 自动在脚本节点右侧创建分镜图组                │
└─────────────────────────────────────────────────────┘
```

---

## 🎨 画布布局方案

采用**左右两列动态布局**：

```
┌──────────────────────────────────────────────────────────────────────────┐
│                                                                           │
│  📋 用户产出区（左列，x=50）               🤖 AI生成区（右列，x=950）        │
│                                                                           │
│  [0] 剧本正文 (y=100)                      剧本 script (y=100)             │
│  [1] 人物设定 (y=450)                            ↓ libtv 自动创建 →       │
│  [2] 场景设定 (y=800)                       分镜图组（脚本右侧）            │
│  [3] 静态封面提示词-竖版 (y=1150)           └ 分镜#1                       │
│  [4] 静态封面提示词-横版 (y=1500)           └ 分镜#2 ...                   │
│  [5] 视频开场提示词-竖版 (y=1850, 按需)                                   │
│  [6] 视频开场提示词-横版 (y=2200, 按需)                                   │
│                                                                           │
│  左列节点数：最少6个，最多7个                                              │
│  y坐标 = 100 + (index × 350)                                              │
└──────────────────────────────────────────────────────────────────────────┘
```

> **注意**：分镜图组由 `libtv script storyboard` 命令自动在脚本节点右侧创建，位置由画布自动排列

---

## 🛠️ LibTV CLI 命令说明

本 Skill 使用 `libtv` CLI 完成所有操作，不构造 HTTP 请求或绕到网页端。

### 常用命令

```bash
# 创建项目
libtv project create "剧名"

# 绑定项目
libtv project use <UUID>

# 创建文本节点（左列，y 坐标按 index 公式计算）
libtv node --x 50 --y <y> create "节点名" -t text -u "content=[\"内容\"]"

# 创建脚本节点（右列）
libtv node --x 950 --y 100 create "剧本" -t script --prompt "<prompt>" --set "model=GVLM 3.1"

# 运行脚本生成
libtv node "剧本" --run

# 生成分镜图
libtv script storyboard "剧本" -s "model=Lib Navo 2" -s "aspectRatio=16:9" -s "settings={\"quality\":\"2K\",\"ratio\":\"16:9\"}"
```

---

## ⚠️ 重要规则

### ⛔ 严禁触发视频生成（红线）

Agent **绝对禁止**执行任何视频生成操作，包括：
- ❌ `libtv script video`
- ❌ 触发 video 节点
- ❌ 渲染导出

> ⚠️ 视频生成成本极高，必须由用户在 LibTV 画布中**手动操作**

### ✅ 分镜图自动生成

本 Skill 使用 `libtv script storyboard` 命令自动生成分镜图，分镜图费用较低，自动生成可节省用户操作。

---

## 📦 安装方式

### 方式一：WorkBuddy 用户

1. 下载 [最新 Release zip](https://github.com/hugohung/workbuddy-skill-jd-ai-short-drama-libtv/releases/latest)
2. 在 WorkBuddy **技能管理** → **上传技能**，选择 zip 文件

### 方式二：从源码安装

```bash
git clone https://github.com/hugohung/workbuddy-skill-jd-ai-short-drama-libtv.git ~/.workbuddy/skills/jd-ai-short-drama-libtv
```

### 安装 LibTV CLI

下载并安装 LibTV CLI：

```
https://liblibai-web-static.liblib.cloud/cli/1.0.2/libtv-cli-skill.zip
```

默认安装路径：
| 平台 | 路径 |
|------|------|
| Windows | `%USERPROFILE%\.libtv\libtv.exe` |
| macOS / Linux | `~/.libtv/libtv` |

详细安装步骤请参考 [libtv-cli 官方文档](https://github.com/hugohung/workbuddy-skill-libtv-cli)。

---

## 🔗 相关 Skill

- [**京东AI短剧剧本助手**](https://github.com/hugohung/workbuddy-skill-jd-ai-short-drama-helper) — 生成符合京东规范的短剧剧本
- [**libtv-cli**](https://github.com/hugohung/workbuddy-skill-libtv-cli) — LibTV 官方 CLI 的 WorkBuddy 集成
- [**热门话题及短剧题材调研专家**](https://github.com/hugohung/workbuddy-skill-drama-topic-research) — 调研短剧题材和热点话题

---

## 🐛 错误处理

### 登录状态检查

在每个涉及 libtv 操作的阶段开始前，先验证登录状态：

```bash
libtv project list
```

如果返回认证错误，提示用户重新登录：

```bash
libtv login web
```

### 节点创建失败

如果某个节点创建失败（CLI 返回错误）：
1. 向用户报告具体错误信息
2. 重试一次（可能是临时网络问题）
3. 如果仍然失败，提示用户可在 libtv 画布中手动创建该节点

---

## ⚠️ 注意事项

1. **禁止自动生成视频** — 本 Skill 绝不执行任何会触发视频生成的操作
2. **禁止修改用户内容** — 从剧本文档提取的内容应原样写入 libtv 节点
3. **JOY 隔离** — 写入 script 节点的 prompt 中严禁出现 JOY 相关描述
4. **坐标一致性** — 左列节点 x=50，AI生成节点 x=950；y 坐标按公式 `y = 100 + (index × 350)` 计算
5. **CLI 命令准确性** — 所有 libtv 命令以 `libtv --help` 的实际输出为准

---

## 📄 License

[MIT License](LICENSE)

---

## 👨‍💻 作者

**honghaoxiang**

- GitHub: [@hugohung](https://github.com/hugohung)
- WorkBuddy: [codebuddy.cn](https://www.codebuddy.cn)

---

## 🙏 致谢

- [LibTV](https://liblib.ai) — 提供 AI 短剧创作平台
- [libtv-cli](https://github.com/hugohung/workbuddy-skill-libtv-cli) — LibTV 官方 CLI 工具

---

**⭐ 如果这个 Skill 对你有帮助，欢迎 Star 和分享！**
