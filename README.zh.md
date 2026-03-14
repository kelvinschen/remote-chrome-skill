# Remote Chrome Skill

在无 GUI 的 Linux 服务器上启动和管理远程 Chrome 浏览器，支持基于 Web 的 VNC 访问。

## Demo

![1](https://github.com/kelvinschen/misc_resources/blob/main/screenshot-20260314-162123.png?raw=true)
![2](https://github.com/kelvinschen/misc_resources/blob/main/screenshot-20260314-162148.png?raw=true)
![3](https://github.com/kelvinschen/misc_resources/blob/main/screenshot-20260314-162222.png?raw=true)


## 安装
`npx skills add kelvinschen/remote-chrome-skill`

## 为什么使用这个工具？

### 🖥️ 在无图形界面的服务器上访问远程浏览器

许多 Linux 服务器（云实例、CI/CD 运行器、开发环境）在没有图形界面的情况下运行（headless 模式）。这个工具解决了在无图形界面服务器上需要使用完整 Chrome 浏览器并进行可视化访问的问题。

**常见使用场景：**
- **云端开发**：在没有桌面环境的 AWS EC2、Google Cloud 或 Azure 实例上运行 Chrome
- **CI/CD 测试**：在自动化测试期间对 Web 应用程序进行视觉验证
- **远程开发**：通过 Web 界面从任何地方访问浏览器
- **网页抓取**：可视化监控抓取操作
- **浏览器自动化测试**：实时观看自动化测试的运行

### 🌐 基于 Web 的访问

与传统的远程桌面解决方案不同，这个工具提供：
- **浏览器访问**：通过任何现代 Web 浏览器连接（无需 VNC 客户端）
- **安全 VNC**：为本地客户端提供密码保护的 VNC 连接
- **多用户支持**：与团队共享浏览器会话

### 🤖 与 agent-browser 集成

此工具专为与 `agent-browser` 技能无缝协作而设计，实现程序化控制：

```bash
# 1. 启动远程 Chrome 并启用调试
./start-remote-chrome.sh

# 2. 连接 agent-browser 控制 Chrome
agent-browser connect --url http://localhost:9222

# 3. 自动化浏览器操作
agent-browser open https://example.com
agent-browser click "#submit-button"
agent-browser type "#input-field" "你好，世界"
```

**组合使用的优势：**
- **可视化监控**：通过 Web VNC 实时观看自动化过程
- **调试自动化**：可视化验证脚本正在执行的操作
- **手动干预**：需要时可以手动接管控制
- **状态跟踪**：使用 `./status-remote-chrome.sh` 查看 agent-browser 打开的所有标签页

### 🛠️ 技术栈

此工具结合了这些强大的技术：

- **Xvfb**：虚拟 X11 显示服务器 - 在没有物理硬件的情况下创建虚拟屏幕
- **x11vnc**：虚拟显示的 VNC 服务器
- **noVNC**：使用 HTML5/WebSockets 的基于 Web 的 VNC 客户端
- **Chrome/Chromium**：具有远程调试功能的完整浏览器

这种组合既提供了可视化访问（通过 Web VNC），也提供了程序化控制（通过 Chrome DevTools 协议）。

## 快速开始

```bash
# 启动服务（自动检查依赖）
cd skills/remote-chrome
./start-remote-chrome.sh

# 检查状态并获取访问 URL
./status-remote-chrome.sh

# 完成后停止服务
./stop-remote-chrome.sh
```

## 访问方式

启动后，您可以通过多种方式访问 Chrome：

### Web 访问（推荐）
```
http://<服务器IP>:6080/vnc.html?autoconnect=true
```
无需安装软件 - 适用于任何现代浏览器。

### VNC 客户端
```
<服务器IP>:5900
```
使用任何 VNC 客户端（Remmina、TigerVNC、RealVNC 等）连接，使用自动生成的密码。

### 远程调试
```
http://<服务器IP>:9222
```
通过 Chrome DevTools 协议连接以进行程序化控制（由 agent-browser 使用）。

## 安装

启动脚本会自动检查依赖项，如果缺少任何内容，会提供安装命令：

**必需软件包：**
- `xvfb` - 虚拟 X 服务器
- `x11vnc` - VNC 服务器
- `novnc` 和 `websockify` - Web VNC 客户端
- `chromium` 或 `google-chrome-stable` - 浏览器

在 Ubuntu/Debian 上安装：
```bash
sudo apt-get install xvfb x11vnc novnc websockify chromium
```

## 配置

### 自定义端口
```bash
./start-remote-chrome.sh --vnc-port 5901 --novnc-port 6081 --chrome-debug-port 9223
```

### 屏幕分辨率
```bash
./start-remote-chrome.sh --screen-size 1920x1080x24
```

### 代理支持
```bash
./start-remote-chrome.sh --proxy http://proxy.example.com:8080 --proxy-bypass "localhost,*.internal.com"
```

## 使用场景

### 1. 在云服务器上开发
```bash
# 在您的云实例上启动 Chrome
./start-remote-chrome.sh

# 从本地浏览器访问
# 打开：http://your-cloud-instance-ip:6080/vnc.html
```

### 2. 带视觉验证的自动化测试
```bash
# 启动浏览器
./start-remote-chrome.sh

# 使用 agent-browser 运行测试
agent-browser connect --url http://localhost:9222
agent-browser open http://your-app.local
# 运行测试自动化...

# 通过 Web VNC 可视化验证测试执行
```

### 3. 远程调试
```bash
# 使用详细日志启动
./start-remote-chrome.sh -v -f

# 实时观看浏览器行为
# 在以下位置访问 DevTools：http://localhost:9222
```

### 4. 团队协作
```bash
# 在共享服务器上启动一次
./start-remote-chrome.sh

# 多个团队成员可以通过 Web 浏览器访问
# 安全地共享 URL 和 VNC 密码
```

## 状态监控

检查远程浏览器的当前状态：

```bash
./status-remote-chrome.sh
```

输出包括：
- 进程状态（Xvfb、x11vnc、noVNC、Chrome）
- 每个组件的内存使用情况
- 打开的 Chrome 标签页（如果 agent-browser 已连接）
- 访问 URL 和 VNC 密码
- 端口可用性

## 安全注意事项

- **VNC 密码**：为每个会话自动生成 12 个字符的密码
- **防火墙**：确保仅暴露必要的端口（5900、6080、9222）
- **HTTPS**：在生产环境中，考虑使用反向代理并启用 SSL/TLS
- **访问控制**：使用防火墙规则限制仅信任的 IP 访问

## 故障排除

### 端口冲突
```bash
# 首先停止现有服务
./stop-remote-chrome.sh

# 或使用自定义端口
./start-remote-chrome.sh --vnc-port 5901 --novnc-port 6081
```

### 缺少依赖项
启动脚本会检测缺失的依赖项并提供安装命令。

### Chrome 无法启动
使用详细模式检查日志：
```bash
./start-remote-chrome.sh -v -f
```

## 文档

有关详细信息，请参阅 `skills/remote-chrome/SKILL.md` 文件，其中包含：
- 完整的命令参考
- 配置选项
- 集成示例
- 故障排除指南
