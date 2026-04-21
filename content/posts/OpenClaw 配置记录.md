# OpenClaw 配置记录

## 1. 主配置文件 (clawdbot.json)

这是 OpenClaw (Clawdbot) 的核心配置文件，位于 `~/.clawdbot/clawdbot.json`。

### 主要配置项解读:

*   **`auth.profiles`**: 定义了认证方式，包括 Google (OAuth 和 API Key)、Ollama、Qwen Portal。
*   **`models.providers`**: 配置了模型提供商，如 Ollama (本地) 和 Qwen Portal (云端)。
*   **`agents.defaults.model.primary`**: 设置了默认主模型为 `google/gemini-3-pro-preview`。
*   **`agents.defaults.model.fallbacks`**: 定义了一系列备用模型，形成一个模型池。
*   **`tools.web.search.enabled`**: 启用了网络搜索功能，并配置了 Brave Search API Key。
*   **`channels.telegram`**: 配置了 Telegram 作为通信渠道。
*   **`gateway.port`**: 设置了 Gateway 的本地端口为 `18789`。

## 2. "强强联合神器 1: OpenClaw + Skills"

*   **操作**: 通过 `clawdbot skills install <skill-name>` 命令安装。
*   **示例**: `bluebubbles` (用于 iMessage)。

## 3. "强强联合神器 2: OpenClaw + 浏览器插件"

*   **插件**: Clawdbot Browser Relay (官方插件)。
*   **配置**: 插件连接到 Gateway 的 `18789` 端口。
*   **权限**: 需要在使用时手动 "Attach to this tab" 才能进行 `snapshot` 和控制。

## 4. "强强联合神器 3: OpenClaw + GitHub 工具"

*   **机制**: 通过 `exec` 命令调用本地安装的 CLI 工具 (如 `gh`, `yt-dlp`)。
*   **示例**: 在 Terminal 中安装 `brew install gh` 后，Clawdbot 即可调用 `gh` 命令。

## 5. "强强联合神器 4: OpenClaw + n8n"

*   **用途**: 构建复杂的自动化工作流，如“调研代理”。
*   **配置**: 在 n8n 中配置 Nodes (Brave Search, LLM, Telegram) 并创建 Workflow。

## 6. 实验性配置: Google Computer Use

*   **目的**: 验证最新的 AI 屏幕操作能力。
*   **步骤**:
    1.  **克隆仓库**: `git clone https://github.com/google/computer-use-preview.git google-computer-use`
    2.  **创建虚拟环境**: `python3 -m venv .venv && source .venv/bin/activate`
    3.  **安装依赖**: `pip install -r requirements.txt`
    4.  **安装 Playwright 浏览器**: `playwright install chromium` (强制安装专用内核)
    5.  **运行**: `export GEMINI_API_KEY="..." && python main.py --query "..." --env playwright`
*   **结果**: 成功运行，AI 能够“看”屏幕并执行操作，包括处理 CAPTCHA 验证。
*   **位置**: `/Users/mark/clawd/google-computer-use/`

## 7. 实验性配置: Browser MCP

*   **目的**: 探索基于 Model Context Protocol (MCP) 的浏览器控制标准。
*   **组件**: Chrome Extension (Browser MCP) + MCP Server (`npx @browsermcp/mcp@latest`).
*   **状态**: Extension 已安装，Server 尚未成功连接至 Clawdbot (需要 MCP Client 支持或桥接脚本)。
*   **位置**: Browser MCP Extension (Chrome Web Store).