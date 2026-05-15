---
theme: default
title: Python MCP 环境搭建与配置实战
info: |
  ## Python 虚拟环境 + MCP 配置完全指南
  从零搭建 Python MCP 开发环境，涵盖虚拟环境、VS Code 配置与 Agent 工具调用。
class: text-center
transition: slide-left
duration: 30min
---

# Python MCP 环境搭建与配置实战

## 从虚拟环境到 Agent 工具调用的完整流程

<div class="pt-12">
  <span class="text-sm opacity-50">2026-05-08 · 技术演示</span>
</div>

<div class="abs-br m-6 flex gap-2">
  <span class="text-xs opacity-40">Space / → 翻页</span>
</div>

<!--
开场自我介绍，简要说明今天演示的主题：从零搭建 Python MCP 开发环境。预计时长 30 分钟，中间有问题可以随时打断。
-->

---
layout: default
transition: fade-out
---

# 目录

<Toc minDepth="1" maxDepth="1" columns="2" />

<!--
整体分为五个部分：环境准备 → MCP 配置 → VS Code 实操 → 异常排查 → 安全规范。每个部分循序渐进，建议按顺序讲解。
-->

---
layout: section
---

# 第一部分

## 环境准备与依赖安装

<!--
第一部分聚焦最基础的环境搭建。确保每位观众都清楚 venv 的必要性，以及如何正确激活和使用虚拟环境。
-->

---
layout: default
---

# Python 虚拟环境搭建

创建隔离的 Python 运行环境，避免依赖冲突：

```bash
# 创建虚拟环境
python -m venv venv

# 激活虚拟环境 (Windows)
venv\Scripts\activate

# 激活虚拟环境 (macOS / Linux)
source venv/bin/activate
```

<v-click>

<div class="mt-6 p-4 border rounded bg-green-50 dark:bg-green-900/20 border-green-300">
  <span class="font-bold text-green-700 dark:text-green-300">✓ 搭建结果：</span>
  虚拟环境创建成功，终端提示符前出现 <code>(venv)</code> 标识，Python 解释器路径指向 <code>venv/Scripts/python.exe</code>
</div>

</v-click>

<!--
强调虚拟环境激活后的终端提示符变化：(venv) 前缀是最直观的成功标志。如果听众中有 macOS/Linux 用户，提醒 source 命令的区别。
-->

---
layout: two-cols
---

# requirements.txt

定义项目依赖清单，统一团队开发环境：

```txt
# requirements.txt
mcp>=1.0.0
playwright>=1.40.0
```

<br>

#### 核心作用

- **版本锁定** — 确保所有开发者使用相同依赖版本
- **一键安装** — `pip install -r requirements.txt`
- **可追溯** — 明确记录项目所需的所有包

::right::

<div class="ml-4 mt-12">

# 执行效果

```bash {all|1|3|4-6}
$ pip install -r requirements.txt

Collecting mcp>=1.0.0
  Downloading mcp-1.x.x-py3-none-any.whl
Collecting playwright>=1.40.0
  Downloading playwright-1.x.x-py3-none-any.whl
Installing collected packages: mcp, playwright

Successfully installed mcp-1.x.x
  playwright-1.x.x
```

<v-click>

<div class="mt-4 p-3 border rounded bg-blue-50 dark:bg-blue-900/20">
  <span class="font-bold">✓ 依赖安装完成</span>，所有包已就绪
</div>

</v-click>

</div>

<!--
requirements.txt 是 Python 项目的"身份证"。强调它应该随 git 一起管理，CI/CD 中也依赖它来还原环境。
-->

---
layout: default
---

# playwright install — 浏览器内核安装

Playwright 需要独立的浏览器二进制文件，`pip install` 仅安装 Python 绑定：

```bash {all|1|3-5|6}
(venv) $ playwright install

Downloading Chromium 120.0.6099.28 (playwright build v1091)
Chromium 120.0.6099.28 downloaded to
  C:\Users\...\AppData\Local\ms-playwright\chromium-1091
Downloading Firefox 121.0 (playwright build v1462)
Firefox 121.0 downloaded to ...
```

<v-click>

<div class="mt-4 grid grid-cols-2 gap-4">

<div class="p-3 border rounded bg-yellow-50 dark:bg-yellow-900/20">
  <span class="font-bold">⚠ 常见误区</span><br>
  <span class="text-sm">只运行 <code>pip install playwright</code> 并不安装浏览器，必须单独执行 <code>playwright install</code></span>
</div>

<div class="p-3 border rounded bg-green-50 dark:bg-green-900/20">
  <span class="font-bold">✓ 安装意义</span><br>
  <span class="text-sm">为 Playwright 提供 Chromium / Firefox / WebKit 内核，实现跨浏览器自动化操作</span>
</div>

</div>

</v-click>

<!--
关键区分：pip install playwright ≠ 安装浏览器。很多新手在这里卡住，务必强调 playwright install 是独立的步骤，CI/CD 中也需要单独执行。
-->

---
layout: section
---

# 第二部分

## MCP 配置文件解析

<!--
第二部分是整个演示的核心。mcp.json 的三个字段看似简单，但每个都有规范要求。重点讲清楚 type/command/args 各自的作用和取值规范。
-->

---
layout: default
---

# .vscode/mcp.json 配置文件

MCP (Model Context Protocol) Server 的核心配置文件，位于项目的 `.vscode` 目录下：

```json {all|2-6|all}
{
  "servers": {
    "xiaohongshu-mcp": {
      "type": "stdio",
      "command": "${workspaceFolder}/venv/Scripts/python.exe",
      "args": ["-m", "xiaohongshu_mcp"]
    }
  }
}
```

<v-click>

<div class="mt-4 text-sm opacity-75">
  配置文件定义了一个名为 <code>xiaohongshu-mcp</code> 的 MCP Server，通过 stdio 协议与本地的 Python 脚本通信。
</div>

</v-click>

<!--
用 JSON 高亮逐行讲解 mcp.json 的结构。强调 servers 对象下每个 key 是 Server 名称，可以配置多个 MCP Server。
-->

---
layout: default
---

# 字段解析 — type

```json {2}
{
  "type": "stdio"
}
```

| 取值 | 含义 | 适用场景 |
|------|------|----------|
| `stdio` | 标准输入/输出通信 | 本地命令行程序、脚本类 MCP Server |
| `sse` | Server-Sent Events | 远程 HTTP 服务、Web 部署的 MCP Server |

<div class="mt-4 p-3 border rounded bg-gray-50 dark:bg-gray-800/50">
  <span class="font-bold">配置目的：</span>声明 MCP Client (VS Code) 与 MCP Server 之间的通信协议。<br>
  <span class="text-sm opacity-75">大多数本地 MCP Server 使用 <code>stdio</code>，通过进程 stdin/stdout 交换 JSON-RPC 消息。</span>
</div>

<!--
type 字段只有 stdio 和 sse 两个取值。对于本地脚本类 MCP Server，几乎都是 stdio。sse 适用于远程服务场景，了解即可。
-->

---
layout: default
---

# 字段解析 — command

```json {2}
{
  "command": "${workspaceFolder}/venv/Scripts/python.exe"
}
```

| 要素 | 说明 |
|------|------|
| `${workspaceFolder}` | VS Code 内置变量，指向当前打开的工作区根目录 |
| `venv/Scripts/python.exe` | 虚拟环境内的 Python 解释器路径 (Windows) |
| `venv/bin/python` | 虚拟环境内的 Python 解释器路径 (macOS / Linux) |

<div class="mt-4 p-3 border rounded bg-blue-50 dark:bg-blue-900/20">
  <span class="font-bold">取值规范：</span>必须指向虚拟环境内的 Python 解释器，确保使用项目专属的依赖包。
</div>

<!--
command 是最容易出错的字段。重点讲解 ${workspaceFolder} 变量的作用——它让配置具备跨机器可移植性，不同开发者 clone 后都能正常运行。
-->

---
layout: default
---

# 字段解析 — args

```json {2}
{
  "args": ["-m", "xiaohongshu_mcp"]
}
```

| 参数 | 含义 |
|------|------|
| `-m` | Python 解释器标志，表示以模块方式运行 |
| `xiaohongshu_mcp` | 要运行的 Python 模块名（即 MCP Server 入口） |

<div class="mt-4 p-3 border rounded bg-gray-50 dark:bg-gray-800/50">
  <span class="font-bold">配置目的：</span>通过 <code>python -m xiaohongshu_mcp</code> 方式启动 MCP Server，等价于在终端中执行该命令。args 数组的每个元素对应命令行的一个参数。
</div>

<v-click>

<div class="mt-3 text-sm">
  <span class="font-bold">扩展形式：</span>如需传参，可追加数组元素，如 <code>"args": ["-m", "xiaohongshu_mcp", "--port", "8080"]</code>
</div>

</v-click>

<!--
args 数组的每个元素对应命令行中的一个空格分隔的参数。-m 表示 module 模式，是 Python 运行模块的标准方式。第二个元素是 MCP Server 的入口模块名。
-->

---
layout: two-cols
---

# 为什么 command 必须指向 venv 内的 python.exe？

<div class="text-sm">

### 必要性分析

1. **依赖隔离** — `xiaohongshu_mcp` 等包安装在 venv 中，系统 Python 无法找到
2. **版本一致性** — venv 锁定 Python 版本和包版本，避免环境差异
3. **权限安全** — venv 内的操作不污染系统级 Python 环境

</div>

::right::

<div class="ml-4">

### 错误配置对比

```json
// ❌ 错误 — 指向系统 Python
"command": "python"

// ❌ 错误 — 绝对路径无变量
"command": "C:\\Python312\\python.exe"

// ✅ 正确 — venv 内解释器
"command": "${workspaceFolder}/venv/Scripts/python.exe"
```

</div>

<v-click>

<div class="mt-2 p-3 border rounded bg-red-50 dark:bg-red-900/20">
  <span class="font-bold">⚠ 错误配置影响：</span>MCP Server 启动失败，报 <code>ModuleNotFoundError: No module named 'xiaohongshu_mcp'</code>
</div>

</v-click>

<!--
这是最常见的配置错误之一。用实际报错信息让观众记住：系统 Python 找不到 venv 内安装的包。正确的 command 路径是这个问题的唯一解法。
-->

---
layout: section
---

# 第三部分

## VS Code 状态确认与工具调用

<!--
第三部分是实操演练。这一页开始引导观众动手操作 VS Code。建议现场演示时同步打开 VS Code，让观众跟着步骤走。
-->

---
layout: default
---

# 在 VS Code 中确认 MCP Server 状态

操作步骤：

1. 打开 VS Code，按 <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd> 打开命令面板
2. 输入 `MCP`，选择 **MCP: List Servers**
3. 在弹出的面板中查看 MCP Server 列表

<div class="mt-6 p-4 border rounded bg-green-50 dark:bg-green-900/20">

### ✓ 确认关键位置

| 检查项 | 预期状态 |
|--------|----------|
| Server 名称 | `xiaohongshu-mcp` |
| 状态标识 | <span class="text-green-600 font-bold">● Running</span> |
| 可用工具数 | 4+ (search_notes, get_note_content, post_comment 等) |

</div>

<!--
Ctrl+Shift+P → MCP: List Servers 是最常用的检查方式。确认三个关键点：名称正确、状态 Running、工具列表完整。任何一项不对都需要回查配置。
-->

---
layout: default
---

# MCP Server "Running" 状态呈现

```text {all}
┌─────────────────────────────────────────────┐
│  MCP Servers                          [✕]   │
├─────────────────────────────────────────────┤
│                                             │
│  ● xiaohongshu-mcp          Running   ✓    │
│    ├─ mcp0_search_notes                    │
│    ├─ mcp0_get_note_content                │
│    ├─ mcp0_post_comment                    │
│    └─ mcp0_analyze_note                    │
│                                             │
└─────────────────────────────────────────────┘
```

<div class="mt-4 flex gap-4">

<div class="flex-1 p-3 border rounded bg-green-50 dark:bg-green-900/20">
  <span class="font-bold text-green-700">● Running</span><br>
  <span class="text-sm">绿色圆点 + "Running" 文字表示 Server 已成功启动并等待指令</span>
</div>

<div class="flex-1 p-3 border rounded bg-red-50 dark:bg-red-900/20">
  <span class="font-bold text-red-700">● Stopped</span><br>
  <span class="text-sm">红色圆点表示 Server 未运行，需检查配置或重新启动</span>
</div>

</div>

<!--
用 ASCII 框图模拟 VS Code 界面，让观众直观感受 Running 状态的样子。绿色圆点 + 展开的工具列表是最重要的确认点。对比 Stopped 状态帮助识别问题。
-->

---
layout: default
---

# Agent 模式工具调用 — mcp0_search_notes

在 VS Code Agent 模式下完整演示一次搜索工具调用：

```text
用户输入:
  搜索小红书上关于 "Python 爬虫教程" 的笔记

Agent 调用:
  mcp0_search_notes
    keywords: "Python 爬虫教程"
    limit: 5
```

<v-click>

```json {all|2-3|4-5|6-12}
{
  "tool": "mcp0_search_notes",
  "parameters": {
    "keywords": "Python 爬虫教程",
    "limit": 5
  },
  "result": [
    {
      "title": "Python 爬虫入门到实战",
      "author": "技术博主小王",
      "likes": 2341,
      "url": "https://www.xiaohongshu.com/..."
    }
  ]
}
```

</v-click>

<!--
这是唯一的完整工具调用演示页。json 代码块用点击动画逐步展示参数结构和返回结果，建议逐行讲解 JSON 各字段含义。
-->

---
layout: default
---

# 搜索实操效果 — 湖北师范大学美食推荐

<div class="grid grid-cols-5 gap-6 mt-2">

<div class="col-span-3">

### 搜索输入

```text
keywords: "湖北师范大学美食推荐"
limit: 10
```

<div class="text-sm mt-1 opacity-75">
在 Agent 对话框中输入自然语言描述，Agent 自动调用 <code>mcp0_search_notes</code> 工具
</div>

### 搜索结果（部分）

| # | 标题 |
|---|------|
| 5 | 湖师食堂总结篇（节选三个食堂） |
| 6 | 湖师干饭必吃榜！ |
| 7 | 湖师食堂探秘——问山居篇 |
| 9 | 在湖师HBNU吃吃吃~ |
| 10 | 重生之我在湖师的第一周半吃什么 |

</div>

<div class="col-span-2 flex flex-col justify-center">
  <img src="/result/搜索湖北师范大学美食.png" class="w-full rounded border shadow object-contain max-h-96" alt="搜索湖北师范大学美食截图" />
  <div class="text-xs text-center mt-1 opacity-50">▲ 实际搜索调用结果界面 — 共返回 10 篇相关笔记</div>
</div>

</div>

<!--
用实际搜索案例"湖北师范大学美食推荐"替代之前的示例"Python 爬虫教程"，展示真实的 Agent 工具调用结果。左侧复现搜索参数，右侧展示返回的关键结果条目，底部附实际界面截图。
-->

---
layout: default
---

# 工具调用操作流程详解

<div class="grid grid-cols-3 gap-3">

<div class="p-3 border rounded">

**Step 1** — 输入指令
<div class="text-sm mt-1">
在 Agent 对话框中输入自然语言描述，如"搜索小红书笔记"
</div>
</div>

<div class="p-3 border rounded">

**Step 2** — 工具选择
<div class="text-sm mt-1">
Agent 自动匹配工具 <code>mcp0_search_notes</code>，填充参数
</div>
</div>

<div class="p-3 border rounded">

**Step 3** — 执行调用
<div class="text-sm mt-1">
MCP Server 接收 JSON-RPC 请求，Playwright 在后台执行搜索
</div>
</div>

<div class="p-3 border rounded">

**Step 4** — 结果返回
<div class="text-sm mt-1">
搜索结果以结构化 JSON 返回，包含标题、作者、链接
</div>
</div>

<div class="p-3 border rounded">

**Step 5** — 结果呈现
<div class="text-sm mt-1">
Agent 将 JSON 解析为可读格式呈现给用户
</div>
</div>

<div class="p-3 border rounded">

**Step 6** — 后续操作
<div class="text-sm mt-1">
用户可进一步要求查看笔记详情、发表评论等
</div>
</div>

</div>

<div class="mt-3 text-center text-sm opacity-75">
  完整调用链：用户 → Agent → MCP Client → MCP Server (Python + Playwright) → 小红书平台 → 返回结果
</div>

<!--
六步流程图覆盖了从用户输入到结果返回的全链路。强调 Step 2 中 Agent 的"自动匹配"能力——用户不需要记住工具名，自然语言即可驱动。
-->

---
layout: default
---

# 浏览器自动化效果展示

<div class="grid grid-cols-5 gap-6 mt-2">

<div class="col-span-3">

<div class="text-sm mb-3">
  当 Agent 调用 <code>mcp0_search_notes</code> 时，Playwright 自动打开浏览器执行搜索操作：
</div>

<div class="grid grid-cols-3 gap-3 text-sm">
<div class="p-2 border rounded bg-blue-50 dark:bg-blue-900/20">
  <span class="font-bold">① 启动浏览器</span><br>
  Playwright 启动 Chromium 内核
</div>
<div class="p-2 border rounded bg-blue-50 dark:bg-blue-900/20">
  <span class="font-bold">② 执行搜索</span><br>
  自动输入关键词并翻页抓取
</div>
<div class="p-2 border rounded bg-blue-50 dark:bg-blue-900/20">
  <span class="font-bold">③ 返回结果</span><br>
  结构化 JSON 回传至 Agent
</div>
</div>

</div>

<div class="col-span-2 flex flex-col justify-center">
  <img src="/result/自动打开浏览器展示效果.png" class="w-full rounded border shadow object-contain max-h-80" alt="自动打开浏览器展示效果" />
  <div class="text-xs text-center mt-1 opacity-50">▲ Playwright 自动启动浏览器 → 打开小红书 → 输入关键词 → 抓取搜索结果</div>
</div>

</div>

<!--
展示 Playwright 自动打开浏览器的实际效果截图。三个步骤说明：启动浏览器 → 执行搜索 → 返回结果。让观众直观理解"MCP 工具调用 = 自动化浏览器操作"的本质。
-->

---
layout: section
---

# 第四部分

## 异常排查与问题解决

<!--
第四部分是实操中一定会遇到的问题。三个典型异常场景由浅入深：路径错误（配置层）→ 浏览器未安装（依赖层）→ 账号未登录（认证层）。每个场景都按"现象→排查→解决"三段式讲解。
-->

---
layout: default
---

# 异常场景模拟 — 路径错误

<div class="p-3 border rounded bg-red-50 dark:bg-red-900/20 mb-4">
  <span class="font-bold">异常现象：</span>MCP Server 始终显示 <span class="text-red-600">● Stopped</span>，无法启动
</div>

```text
[Error] Failed to start MCP server "xiaohongshu-mcp":
  spawn python ENOENT
```

<v-click>

**排查思路：**

1. 检查 `.vscode/mcp.json` 中的 `command` 路径是否正确
2. 确认虚拟环境是否已创建，`venv/Scripts/python.exe` 是否存在
3. 使用 `${workspaceFolder}` 变量是否正确展开

</v-click>

<v-click>

**解决步骤：**

```bash
# 1. 确认 venv 路径
ls venv/Scripts/python.exe     # Windows
ls venv/bin/python             # macOS / Linux

# 2. 修正为绝对路径或正确的相对变量路径
"command": "${workspaceFolder}/venv/Scripts/python.exe"
```

</v-click>

<!--
ENOENT 错误表示"可执行文件不存在"。排查的核心思路：先确认 venv 是否存在 → 再确认 python.exe 路径 → 最后检查 ${workspaceFolder} 变量展开是否正确。用 click 动画分步展示排查过程。
-->

---
layout: default
---

# 异常场景模拟 — 浏览器内核未安装

<div class="p-3 border rounded bg-red-50 dark:bg-red-900/20 mb-4">
  <span class="font-bold">异常现象：</span>工具调用报错，提示找不到浏览器可执行文件
</div>

```text
playwright._impl._errors.Error:
  Executable doesn't exist at
  C:\Users\...\ms-playwright\chromium-1091\chrome-win\chrome.exe
```

<div class="mt-4 grid grid-cols-2 gap-4">

<div>

**排查思路：**

- 确认是否运行过 `playwright install`
- 检查磁盘空间是否充足
- 网络是否可访问 Playwright CDN

</div>

<div>

**解决步骤：**

```bash
# 重新安装浏览器内核
playwright install

# 或指定单个浏览器
playwright install chromium

# 检查安装状态
playwright install --dry-run
```

</div>

</div>

<!--
浏览器内核未安装的报错信息非常明确：Executable doesn't exist + 具体路径。用两栏布局对比排查思路和解决步骤。提醒：playwright install 可能需要稳定的网络环境。
-->

---
layout: default
---

# 异常场景模拟 — 账号未登录

<div class="p-3 border rounded bg-yellow-50 dark:bg-yellow-900/20 mb-4">
  <span class="font-bold">异常现象：</span>搜索返回空结果，或提示需要登录
</div>

```json
{
  "error": "Authentication required",
  "message": "请先登录小红书账号后再执行操作"
}
```

<v-click>

**排查与解决：**

```bash
# Step 1: 运行登录命令
python -m xiaohongshu_mcp --login

# Step 2: 在弹出的浏览器中扫码登录

# Step 3: 确认登录状态
python -m xiaohongshu_mcp --status
# 输出: ✓ Logged in as "your_username"
```

</v-click>

<v-click>

<div class="mt-3 p-3 border rounded bg-green-50 dark:bg-green-900/20">
  <span class="font-bold">✓ 恢复效果：</span>登录成功后，所有工具恢复正常。搜索返回完整结果，评论功能可用。
</div>

</v-click>

<!--
账号未登录的排查最为简单但容易被忽略。三步解决：运行登录命令 → 扫码 → 确认状态。强调登录态有时效性，长期不用需要重新登录。
-->

---
layout: section
---

# 第五部分

## 安全规范与合规说明

<!--
最后一部分是必须强调的安全底线。三列分别对应：内容合规、账号边界、风险提示。每一列用清晰条目呈现，适合逐条讲解。最后的大提醒框用橙色高亮，不可跳过。
-->

---
layout: default
---

# 平台合规要求

<div class="grid grid-cols-3 gap-4 mt-8">

<div class="p-4 border rounded text-center">

### 📋 评论内容合规

<div class="text-sm mt-3 text-left">

- 评论内容须符合小红書社区规范
- 禁止发布违规、敏感或侵权内容
- 使用 `post_smart_comment` 时选择合规的评论类型
- 避免批量重复评论行为

</div>

</div>

<div class="p-4 border rounded text-center">

### 🔐 账号操作边界

<div class="text-sm mt-3 text-left">

- 仅操作本人授权账号
- 不得使用他人账号或共享凭证
- 登录态定期刷新，避免会话泄露
- 遵循平台 API 频率限制

</div>

</div>

<div class="p-4 border rounded text-center">

### ⚠ 自动化风险提示

<div class="text-sm mt-3 text-left">

- 高频调用可能触发平台风控
- 异常流量会导致账号限流或封禁
- 建议设置合理的调用间隔
- 仅用于学习研究与合法运营

</div>

</div>

</div>

<div class="mt-6 p-3 border rounded bg-orange-50 dark:bg-orange-900/20 text-center">
  <span class="font-bold">重要提醒：</span>MCP 工具仅用于合法的自动化测试与个人账号运营，严禁用于恶意爬取、水军刷量等违规行为。
</div>

<!--
这一页的信息密度最高。三列分别讲内容合规、账号边界、风险提示。每列控制在 4 条以内，便于阅读。最后用橙色框做总结警示，颜色与正常信息区分。
-->

---
layout: default
---

# 最佳实践总结

<div class="grid grid-cols-2 gap-3 mt-2">

<div class="p-3 border rounded">

#### 环境管理

<div class="text-sm">

- ✅ 始终使用虚拟环境隔离项目依赖
- ✅ `requirements.txt` 随代码一起版本管理
- ✅ 定期 `pip freeze > requirements.txt` 更新锁文件
- ✅ `playwright install` 在 CI/CD 中作为独立步骤

</div>

</div>

<div class="p-3 border rounded">

#### 配置规范

<div class="text-sm">

- ✅ `command` 字段使用 `${workspaceFolder}` 变量
- ✅ `args` 数组清晰分隔每个参数
- ✅ 配置完成后立即验证 Server 状态
- ✅ 敏感信息使用环境变量，不硬编码

</div>

</div>

<div class="p-3 border rounded">

#### 安全准则

<div class="text-sm">

- ✅ 评论内容先审核再发布
- ✅ 设置调用频率上限
- ✅ 定期检查账号状态
- ✅ 记录操作日志便于审计

</div>

</div>

<div class="p-3 border rounded">

#### 异常处理

<div class="text-sm">

- ✅ 遇到错误先检查配置和路径
- ✅ 查看 VS Code 输出面板获取详细日志
- ✅ 登录态过期时重新认证
- ✅ 浏览器内核定期更新

</div>

</div>

</div>

<!--
最佳实践总结用四宫格卡片呈现，方便拍照留存。每一类准则控制在 4 条，简洁易记。可以在讲解时说"这一页建议拍照"。
-->

---
layout: center
class: text-center
---

# 总结

<div class="text-left mx-auto max-w-lg mt-8">

1. **虚拟环境** — 项目依赖隔离的基础，`venv` + `requirements.txt` 是标准组合
2. **MCP 配置** — `type` / `command` / `args` 三字段精准控制 Server 启动
3. **状态确认** — VS Code 面板直观展示 Running/Stopped 状态
4. **工具调用** — Agent 模式下自然语言即可驱动 MCP 工具链
5. **安全合规** — 遵守平台规则，合理使用自动化能力

</div>

<div class="mt-12 text-sm opacity-50">
  配置正确 → 运行稳定 → 调用可靠 → 安全合规
</div>

<!--
总结页回到核心要点。五个要点对应五个部分，用一句话串联整个演示的逻辑闭环。底部的一行文字"配置正确 → 运行稳定 → 调用可靠 → 安全合规"是整场演示的核心金句。
-->

---
layout: center
class: text-center
---

# Q & A

## 谢谢观看

<div class="mt-8 text-sm opacity-40">
  Press <kbd>Esc</kbd> for overview · <kbd>Space</kbd> for next slide
</div>

<!--
演示到此结束。感谢大家的时间。欢迎提问，也可以演示结束后单独交流。相关代码和配置文件已放在项目仓库中供参考。
-->
