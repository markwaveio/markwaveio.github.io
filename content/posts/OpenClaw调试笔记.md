
## 🛠️ 2026-02-01 系统级架构升级复盘

### 1. 问题根源：幽灵代理与脆弱进程
*   **现象**：
    *   `fetch failed` 导致 `Unhandled Rejection`，进程直接 Crash。
    *   虽然 Shell 里配了 Proxy，但 Node 子进程（尤其是 `undici` 库）有时会绕过，导致直连被墙。
    *   手工启动 (`node run`) 没有守护机制，挂了就挂了，无法自愈。

### 2. 解决方案：Mac Launchd 守护进程
弃用手动终端启动，改为 macOS 原生的 **Launch Agent** 服务。

**核心配置文件**: `~/Library/LaunchAgents/bot.clawd.gateway.plist`

**关键配置项解析**：
1.  **强制代理注入**：
    ```xml
    <key>EnvironmentVariables</key>
    <dict>
        <key>HTTP_PROXY</key>
        <string>http://127.0.0.1:7890</string>
    </dict>
    ```
    *原理*：在进程启动的瞬间，在最底层注入环境变量，确保 Node 及其所有子进程无处可逃，必须走代理。

2.  **不死之身 (KeepAlive)**：
    ```xml
    <key>KeepAlive</key>
    <true/>
    ```
    *原理*：无论是因为 OOM 还是 Network Crash，只要进程号消失，launchd 会在几秒内重新拉起。

### 3. 验证结果
*   **PID 检查**: `launchctl list | grep clawd` 返回有效 PID。
*   **网络检查**: `curl google.com` 返回 200 OK。
*   **稳定性**: 即使手动 `pkill`，也会立即复活。

---
if you need to manually control it (restart, stop, update config), here are the commands you should use:

**1. Restart (Apply Config Changes / Fix Glitches)**
```
launchctl kickstart -k gui/$(id -u)/com.clawdbot.gateway
```
_(This is the modern, preferred way to restart a service on macOS without unloading/loading)_


**2. Stop (Disable Temporarily)**
```
launchctl unload -w ~/Library/LaunchAgents/com.clawdbot.gateway.plist
```

**3. Start (Enable Again)**
```
launchctl load -w ~/Library/LaunchAgents/com.clawdbot.gateway.plist
```

**4. View Logs (Debug)**
```
tail -f ~/.clawdbot/gateway.log
# Or for errors:
tail -f ~/.clawdbot/gateway.error.log
```


**Summary:**

  

• **System Reboot**: Do nothing. It starts automatically.

• **Manual Restart**: launchctl kickstart -k ... (or just kill it with pkill -f clawdbot, and it will respawn automatically).


浏览器插件调试