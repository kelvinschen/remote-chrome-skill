# Open Remote Chrome Skill

Launch and manage a remote Chrome browser with web-based VNC access on Linux servers without a GUI.

# Demo

![1](https://github.com/kelvinschen/misc_resources/blob/main/screenshot-20260314-162123.png?raw=true)
![2](https://github.com/kelvinschen/misc_resources/blob/main/screenshot-20260314-162148.png?raw=true)
![3](https://github.com/kelvinschen/misc_resources/blob/main/screenshot-20260314-162222.png?raw=true)
## Why Use This?

### 🖥️ Remote Browser Access on Headless Servers

Many Linux servers (cloud instances, CI/CD runners, development environments) run without a graphical interface (headless). This skill solves the problem of needing a full Chrome browser with visual access on such servers.

**Common scenarios:**

- **Cloud development**: Running Chrome on AWS EC2, Google Cloud, or Azure instances without desktop environments
- **CI/CD testing**: Visual verification of web applications during automated testing
- **Remote development**: Access a browser from anywhere via web interface
- **Web scraping**: Visual monitoring of scraping operations
- **Browser automation testing**: Watching automated tests run in real-time

### 🌐 Web-Based Access

Unlike traditional remote desktop solutions, this skill provides:

- **Browser-based access**: Connect via any modern web browser (no VNC client required)
- **Secure VNC**: Password-protected VNC connections for local clients
- **Multi-user support**: Share the browser session with your team

### 🤖 Integration with agent-browser

This skill is designed to work seamlessly with the `agent-browser` skill for programmatic control:

```bash
# 1. Start remote Chrome with debugging enabled
./start-remote-chrome.sh

# 2. Connect agent-browser to control Chrome
agent-browser connect --url http://localhost:9222

# 3. Automate browser actions
agent-browser open https://example.com
agent-browser click "#submit-button"
agent-browser type "#input-field" "Hello World"
```

**Benefits of the combination:**

- **Visual monitoring**: Watch your automation in real-time via web VNC
- **Debug automation**: Visually verify what your scripts are doing
- **Manual intervention**: Take control manually when needed
- **Status tracking**: Use `./status-remote-chrome.sh` to see all tabs opened by agent-browser

### 🛠️ Technical Stack

The skill combines these powerful technologies:

- **Xvfb**: Virtual X11 display server - creates a virtual screen without physical hardware
- **x11vnc**: VNC server for the virtual display
- **noVNC**: Web-based VNC client using HTML5/WebSockets
- **Chrome/Chromium**: Full-featured browser with remote debugging enabled

This combination provides both visual access (via web VNC) and programmatic control (via Chrome DevTools Protocol).

## Quick Start

```bash
# Start the service (auto-checks dependencies)
cd skills/remote-chrome
./start-remote-chrome.sh

# Check status and get access URLs
./status-remote-chrome.sh

# Stop the service when done
./stop-remote-chrome.sh
```

## Access Methods

Once started, you can access Chrome in multiple ways:

### Web Access (Recommended)

```
http://<server-ip>:6080/vnc.html?autoconnect=true
```

No software installation required - works in any modern browser.

### VNC Client

```
<server-ip>:5900
```

Connect with any VNC client (Remmina, TigerVNC, RealVNC, etc.) using the auto-generated password.

### Remote Debugging

```
http://<server-ip>:9222
```

Connect via Chrome DevTools Protocol for programmatic control (used by agent-browser).

## Installation

The start script automatically checks for dependencies and provides installation commands if anything is missing:

**Required packages:**

- `xvfb` - Virtual X server
- `x11vnc` - VNC server
- `novnc` and `websockify` - Web VNC client
- `chromium` or `google-chrome-stable` - Browser

Install on Ubuntu/Debian:

```bash
sudo apt-get install xvfb x11vnc novnc websockify chromium
```

## Configuration

### Custom Ports

```bash
./start-remote-chrome.sh --vnc-port 5901 --novnc-port 6081 --chrome-debug-port 9223
```

### Screen Resolution

```bash
./start-remote-chrome.sh --screen-size 1920x1080x24
```

### Proxy Support

```bash
./start-remote-chrome.sh --proxy http://proxy.example.com:8080 --proxy-bypass "localhost,*.internal.com"
```

## Use Cases

### 1. Development on Cloud Servers

```bash
# Start Chrome on your cloud instance
./start-remote-chrome.sh

# Access from your local browser
# Open: http://your-cloud-instance-ip:6080/vnc.html
```

### 2. Automated Testing with Visual Verification

```bash
# Start browser
./start-remote-chrome.sh

# Run your tests with agent-browser
agent-browser connect --url http://localhost:9222
agent-browser open http://your-app.local
# Run test automation...

# Visually verify test execution via web VNC
```

### 3. Remote Debugging

```bash
# Start with verbose logging
./start-remote-chrome.sh -v -f

# Watch browser behavior in real-time
# Access DevTools at: http://localhost:9222
```

### 4. Team Collaboration

```bash
# Start once on a shared server
./start-remote-chrome.sh

# Multiple team members can access via web browser
# Share the URL and VNC password securely
```

## Status Monitoring

​​

Check the current state of your remote browser:

```bash
./status-remote-chrome.sh
```

Output includes:

- Process status (Xvfb, x11vnc, noVNC, Chrome)
- Memory usage for each component
- Open Chrome tabs (if agent-browser is connected)
- Access URLs and VNC password
- Port availability

## Security Considerations

- **VNC Password**: Auto-generated 12-character password for each session
- **Firewall**: Ensure only necessary ports (5900, 6080, 9222) are exposed
- **HTTPS**: For production use, consider reverse proxy with SSL/TLS
- **Access Control**: Use firewall rules to limit access to trusted IPs

## Troubleshooting

### Port Conflicts

```bash
# Stop existing service first
./stop-remote-chrome.sh

# Or use custom ports
./start-remote-chrome.sh --vnc-port 5901 --novnc-port 6081
```

### Dependencies Missing

The start script will detect missing dependencies and provide installation commands.

### Chrome Won't Start

Check logs with verbose mode:

```bash
./start-remote-chrome.sh -v -f
```

## Documentation

For detailed information, see the `skills/remote-chrome/SKILL.md` file which includes:

- Complete command reference
- Configuration options
- Integration examples
- Troubleshooting guide

