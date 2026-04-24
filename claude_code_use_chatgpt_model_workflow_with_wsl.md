# Windows + WSL + Claude Code + CLIProxyAPI 使用 ChatGPT 中转流程说明

> 本文档用于说明如何在 **Windows 的 WSL 环境中运行 Claude Code**，并通过 **CLIProxyAPI** 将 Claude Code 的请求中转到 **ChatGPT / OpenAI / GPT 模型**。
>
> 推荐阅读方式：先查看总览流程图，再按照 8 个步骤逐步配置。

---

## 0. 总览流程图

![Windows + WSL + Claude Code + CLIProxyAPI 总览流程图](./image/wsl_claude_code_chatgpt_workflow_chart.png)

---

## 1. Windows 环境准备

本步骤的目标是准备 Windows 侧的基础开发环境，为后续 WSL、VS Code 和 Claude Code 使用打好基础。

### 1.1 推荐环境

建议使用：

| 项目 | 推荐 |
|---|---|
| 操作系统 | Windows 10 / Windows 11 |
| Linux 子系统 | WSL2 |
| Linux 发行版 | Ubuntu / Debian |
| 编辑器 | VS Code |
| 终端 | Windows Terminal |
| AI 编程工具 | Claude Code |
| API 中转工具 | CLIProxyAPI |

---

### 1.2 启用 WSL

在 Windows PowerShell 中执行：

```powershell
wsl --install
```

如果已经安装过 WSL，可以查看当前版本：

```powershell
wsl --version
```

查看已安装的 Linux 发行版：

```powershell
wsl -l -v
```

如果显示类似下面内容，说明 WSL 已经安装成功：

```text
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

---

### 1.3 安装 Ubuntu

如果还没有安装 Ubuntu，可以使用：

```powershell
wsl --install -d Ubuntu
```

也可以在 Microsoft Store 中搜索并安装：

```text
Ubuntu
```

---

### 1.4 安装 VS Code

建议安装：

- Visual Studio Code
- Remote - WSL 插件

VS Code 主要用于在 Windows 上编辑 WSL 中的 Linux 项目。

安装完成后，后续可以在 WSL 项目目录中使用：

```bash
code .
```

直接用 VS Code 打开当前 Linux 项目。

---

## 2. 启动并进入 WSL

本步骤的目标是进入 Linux 子系统，并确认当前终端确实处于 WSL 环境中。

---

### 2.1 打开 Windows Terminal

在 Windows 中打开：

```text
Windows Terminal
```

然后选择：

```text
Ubuntu
```

或者在 PowerShell 中直接执行：

```powershell
wsl
```

---

### 2.2 确认当前处于 Linux 子系统

进入 WSL 后，执行：

```bash
uname -a
```

如果输出中包含：

```text
Linux
Microsoft
WSL
```

说明当前已经进入 WSL 环境。

也可以执行：

```bash
lsb_release -a
```

查看 Linux 发行版信息。

---

### 2.3 推荐项目目录

建议把项目放在 WSL 的 Linux 文件系统中，例如：

```bash
mkdir -p ~/Project
cd ~/Project
```

推荐路径：

```text
/home/你的用户名/Project/your_project
```

不推荐长期把项目放在：

```text
/mnt/c/Users/你的用户名/Desktop/your_project
```

原因是 `/mnt/c` 属于 Windows 文件系统挂载路径，在大量文件读写、依赖安装、Git 操作时可能比 Linux 原生目录慢。

---

## 3. 在 WSL 中安装 Node.js 和 Claude Code

本步骤的目标是在 WSL 中准备 Claude Code 所需的 Node.js 环境，并安装 Claude Code。

---

### 3.1 安装 Node.js / npm

建议使用 `nvm` 安装 Node.js。

先检查是否已经安装：

```bash
node -v
npm -v
```

如果没有安装，可以安装 `nvm` 后再安装 Node.js。

示例：

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
```

使配置生效：

```bash
source ~/.bashrc
```

安装 Node.js LTS 版本：

```bash
nvm install --lts
nvm use --lts
```

再次检查：

```bash
node -v
npm -v
```

---

### 3.2 安装 Claude Code

在 WSL 中安装 Claude Code：

```bash
npm install -g @anthropic-ai/claude-code
```

检查是否安装成功：

```bash
claude --version
```

如果能正常输出版本号，说明 Claude Code 安装成功。

---

### 3.3 启动 Claude Code 测试

执行：

```bash
claude
```

首次运行时，Claude Code 可能会要求登录或进行初始化配置。

此时先不要急着配置中转，先确认：

```text
claude 命令本身可以正常启动
```

---

## 4. 安装并启动 CLIProxyAPI

本步骤的目标是安装 CLIProxyAPI，并让它在本地启动一个代理服务，用来接收 Claude Code 的请求。

---

### 4.1 CLIProxyAPI 的作用

CLIProxyAPI 在本流程中的作用是：

```text
Claude Code
    ↓
ANTHROPIC_BASE_URL 指向 CLIProxyAPI
    ↓
CLIProxyAPI 接收 Anthropic 风格请求
    ↓
CLIProxyAPI 转发到 ChatGPT / OpenAI / GPT 模型
    ↓
返回结果给 Claude Code
```

也就是说：

```text
Claude Code 不直接调用 OpenAI
而是通过 CLIProxyAPI 间接调用 ChatGPT / GPT 模型
```

---

### 4.2 安装 CLIProxyAPI

CLIProxyAPI 的安装方式可能会随版本更新而变化，建议以项目官方 README 为准。

常见安装方式包括：

```text
1. 下载 Release 二进制文件
2. 使用 Docker 运行
3. 从源码编译
4. 使用项目提供的安装脚本
例：curl -fsSL https://raw.githubusercontent.com/brokechubb/cliproxyapi-installer/refs/heads/master/cliproxyapi-installer | bash
```

以下命令基于官方的安装脚本，安装完成后，需要确认命令是否可用。

示例：

```bash
cd ~/cliproxyapi/
~/cliproxyapi/cli-proxy-api --version
```

打开``.bashrc``文件：

```bash
vim ~/.bashrc
```

将以下命令加在``.bashrc``文件末尾：

```text
export PATH=$PATH:/home/你的用户名/cliproxyapi
```

再重新加载配置文件：

```bash
source ~/.bashrc
```

检查环境变量：

```bash
cli-proxy-api --version
which cli-proxy-api
```

---

### 4.3 准备配置文件

CLIProxyAPI 通常需要配置：

```text
监听地址
监听端口
访问 Token
上游模型提供方
OpenAI / ChatGPT 认证方式
模型映射关系
日志等级
```

备份原始的配置文件，例：

```bash
cd ~/cliproxyapi
cp config.yaml config.yaml.rawbak
```

``config.yaml``示意配置如下：

```yaml
server:
  host: 127.0.0.1
  port: 8317

auth:
  token: your_proxy_token

providers:
  openai:
    type: openai
    auth_type: oauth
    default_model: gpt-5.2

routes:
  anthropic:
    default_model: gpt-5.2
```

> 注意：上面的配置只是结构示例，不一定等同于你当前版本 CLIProxyAPI 的真实配置格式。实际配置请以 CLIProxyAPI 官方文档或 README 为准。

**实际只需要更改原始``config.yaml``中的两处位置，例**

```bash
vim config.yaml
```

第一处：

```yaml
remote-management:
# Whether to allow remote (non-localhost) management access.
# When false, only localhost can access management endpoints (a key is still required).
  allow-remote: true  # 将此处允许远程访问接口改为true
# Management key. If a plaintext value is provided here, it will be hashed on startup.
# All management requests (even from localhost) require this key.
# Leave empty to disable the Management API entirely (404 for all /v0/management routes).
  secret-key: "******"  # 此处设置密钥，所有管理请求（包括本地）都需要该密钥，请记录此密钥用于登录管理界面
```

第二处：
```yaml
# Proxy URL. Supports socks5/http/https protocols. Example: socks5://user:pass@192.168.1.1:1080/
# Per-entry proxy-url also supports "direct" or "none" to bypass both the global proxy-url and environment proxies explicitly.
proxy-url: "******"  # 将此处设置为你自己的代理端口
```

---

### 4.4 启动 CLIProxyAPI

示例启动命令：

```bash
cd ~/cliproxyapi
./cli-proxy-api
```

或作为系统服务运行

```bash
# 设置开机自启
systemctl --user enable cliproxyapi.service
# 启动服务
systemctl --user start cliproxyapi.service
# 查看运行状态
systemctl --user status cliproxyapi.service
```

或指定配置文件：

```bash
cli-proxy-api --config ~/cliproxyapi/config.yaml
```

如果你使用 Docker，则可能类似：

```bash
docker run -p 8317:8317 your-cliproxyapi-image
```

---

### 4.5 确认代理端口是否启动

查看端口监听：

```bash
ss -lntp | grep 8317
```

如果看到类似输出：

```text
LISTEN 0 4096 127.0.0.1:8317
```

说明 CLIProxyAPI 已经在本地监听。

也可以使用：

```bash
curl http://127.0.0.1:8317
```

检查服务是否有响应。

---

## 5. 配置上游模型认证

本步骤的目标是让 CLIProxyAPI 能够访问 ChatGPT / OpenAI / GPT 模型。

---

### 5.1 认证方式

CLIProxyAPI 常见上游认证方式包括：

```text
API Key
OAuth 登录
本地账号会话
多账号轮询
OpenAI 兼容 Provider
```

如果你要使用 ChatGPT / OpenAI / Codex 类模型，需要先在 CLIProxyAPI 中完成对应认证。

---

### 5.2 配置 OpenAI / ChatGPT 认证——OAuth登录（两种方式都可行）

#### 5.2.1 设置身份认证（WSL端）

根据 CLIProxyAPI 的实际用法，可能会有类似命令：

```bash
cli-proxy-api --login           # For Gemini
cli-proxy-api --codex-login     # For OpenAI
cli-proxy-api --claude-login    # For Claude
cli-proxy-api --qwen-login      # For Qwen
cli-proxy-api --iflow-login     # For iFlow
```
>选项：加上``--no-browser``可打印登录地址而不自动打开浏览器，复制链接后浏览器打开进行相应的身份认证。

#### 5.2.2 设置身份认证（网页管理端）

Windows的浏览器打开管理页面：

```text
http://localhost:8317/management.html
```
初次登录需要输入``4.3``中第一处修改的``secret-key``

登录后点击页面的``OAuth登录``，选择相对应的模型供应商，有``Codex, Anthropic, Antigravity, Gemini CLI, Kimi``可供选择，点击``登录``后进行相应的身份认证，其实就是打开方法一的链接

---

## 6. 配置 Claude Code 指向 CLIProxyAPI

本步骤的目标是让 Claude Code 不再直接访问默认 Anthropic 服务，而是把请求发送给本地 CLIProxyAPI。

---

### 6.1 临时环境变量配置

在 WSL 终端中执行：

注：``your_proxy_token``在``config.yaml``文件中的``api-keys``位置，又或者在管理页面的``配置面板``中``认证配置``的``API密钥``处

```bash
export ANTHROPIC_BASE_URL="http://127.0.0.1:8317"
export ANTHROPIC_AUTH_TOKEN="your_proxy_token"
export API_TIMEOUT_MS="3000000"
```

各变量含义：

| 环境变量 | 作用 |
|---|---|
| `ANTHROPIC_BASE_URL` | 指定 Claude Code 请求的 API 地址 |
| `ANTHROPIC_AUTH_TOKEN` | 指定代理服务使用的 Bearer Token |
| `API_TIMEOUT_MS` | 设置 API 请求超时时间，适合代理链路较慢时使用 |

检查是否生效：

```bash
env | grep ANTHROPIC
```

正常应看到：

```text
ANTHROPIC_BASE_URL=http://127.0.0.1:8317
ANTHROPIC_AUTH_TOKEN=your_proxy_token
```

---

### 6.2 写入 shell 配置文件

如果临时配置测试成功，可以写入 `~/.bashrc`：

```bash
vim ~/.bashrc
```

添加：

```bash
export ANTHROPIC_BASE_URL="http://127.0.0.1:8317"
export ANTHROPIC_AUTH_TOKEN="your_proxy_token"
export ANTHROPIC_MODEL=gpt-5.3-codex  
export ANTHROPIC_SMALL_FAST_MODEL=gpt-5.4-mini
export ANTHROPIC_DEFAULT_OPUS_MODEL=gpt-5.4
export ANTHROPIC_DEFAULT_SONNET_MODEL=gpt-5.4
```
>具体的模型名称可以在管理网页的``认证文件``的``模型``处得知

保存后执行：

```bash
source ~/.bashrc
```

如果你使用的是 zsh，则写入：

```bash
~/.zshrc
```

---

### 6.3 使用 Claude Code settings.json 配置

也可以使用 Claude Code 的配置文件。

用户级配置文件：

```text
~/.claude/settings.json
```

项目级配置文件：

```text
.claude/settings.json
```

本地项目配置文件：

```text
.claude/settings.local.json
```

示例：

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "http://127.0.0.1:8317",
    "ANTHROPIC_AUTH_TOKEN": "your_proxy_token",
    "API_TIMEOUT_MS": "3000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1"
  }
}
```

推荐优先级：

```text
先用临时 export 测试
        ↓
测试成功后写入 ~/.bashrc
        ↓
如果希望项目级固定配置，再写入 .claude/settings.local.json
```

---

### 6.4 检查配置冲突

如果你之前配置过其他代理，例如：

```text
智谱 GLM
Kimi
OpenRouter
其他 Anthropic 兼容接口
```

需要检查是否还残留旧配置。

检查 shell 环境变量：

```bash
env | grep ANTHROPIC
```

检查 Claude Code 配置文件：

```bash
cat ~/.claude/settings.json
```

如果项目中也有配置，继续检查：

```bash
cat .claude/settings.json
cat .claude/settings.local.json
```

---

## 7. 在 VS Code / WSL 中运行 Claude Code

本步骤的目标是在 VS Code 的 WSL 环境中正常使用 Claude Code。

---

### 7.1 进入项目目录

建议项目放在 WSL 的 Linux 路径中：

```bash
cd ~/Project/your_project
```

如果没有项目，可以创建一个测试项目：

```bash
mkdir -p ~/Project/claude-code-test
cd ~/Project/claude-code-test
```

---

### 7.2 使用 VS Code 打开 WSL 项目

在 WSL 终端中执行：

```bash
code .
```

如果 VS Code 正常打开，并且左下角显示类似：

```text
WSL: Ubuntu
```

说明 VS Code 已经连接到 WSL 环境。

---

### 7.3 检查 VS Code 终端环境

在 VS Code 中打开终端，执行：

```bash
pwd
```

如果路径类似：

```text
/home/your_name/Project/your_project
```

说明当前是 WSL 路径。

继续检查：

```bash
which python
which node
which claude
```

推荐结果类似：

```text
/usr/bin/python
/home/your_name/.nvm/versions/node/xxx/bin/node
/home/your_name/.nvm/versions/node/xxx/bin/claude
```

如果出现 Windows 路径，例如：

```text
C:\Users\xxx
```

说明 VS Code 当前可能没有正确进入 WSL Remote 环境。

---

### 7.4 启动 Claude Code

在 VS Code 的 WSL 终端中执行：

```bash
claude
```

然后输入一个简单任务测试：

```text
请帮我解释当前项目的目录结构
```

如果 Claude Code 能正常返回结果，说明：

```text
VS Code
    ↓
WSL
    ↓
Claude Code
    ↓
CLIProxyAPI
    ↓
ChatGPT / GPT 模型
```

链路基本打通。

---

## 8. 通过 CLIProxyAPI 调用 ChatGPT 并返回结果

本步骤的目标是理解完整请求链路，并验证最终使用效果。

---

### 8.1 完整请求链路

完整链路如下：

```text
用户在 Claude Code 中输入任务
        ↓
Claude Code 构造 Anthropic 风格请求
        ↓
请求发送到 ANTHROPIC_BASE_URL
        ↓
CLIProxyAPI 接收请求
        ↓
CLIProxyAPI 根据配置进行认证和模型映射
        ↓
CLIProxyAPI 转发到 ChatGPT / OpenAI / GPT 模型
        ↓
模型生成结果
        ↓
CLIProxyAPI 返回响应
        ↓
Claude Code 在终端中展示结果
```

---

### 8.2 简单测试任务

可以在 Claude Code 中输入：

```text
请读取当前目录，帮我总结这个项目的结构。
```

或者：

```text
请帮我写一个 Python 脚本，读取 input.txt 并统计每一行的长度。
```

如果能正常返回结果，说明模型调用成功。

---

### 8.3 测试是否真的经过 CLIProxyAPI

可以通过以下方式判断：

1. 查看 CLIProxyAPI 的运行日志。
2. 观察是否有新的请求记录。
3. 停止 CLIProxyAPI 后再次运行 Claude Code，看是否无法请求。
4. 修改 `ANTHROPIC_BASE_URL` 后观察请求是否变化。

例如停止 CLIProxyAPI：

```bash
pkill cliproxyapi
```

然后运行：

```bash
claude
```

如果 Claude Code 报连接错误，说明它确实依赖 CLIProxyAPI。

---

# 9. 常见问题与排查

---

## 9.1 Auth conflict：同时存在 token 和 API key

如果出现类似：

```text
Auth conflict: Both a token and an API key are set
```

通常说明你同时设置了：

```text
ANTHROPIC_AUTH_TOKEN
ANTHROPIC_API_KEY
```

如果你想走 CLIProxyAPI，推荐保留：

```bash
ANTHROPIC_BASE_URL
ANTHROPIC_AUTH_TOKEN
```

取消：

```bash
ANTHROPIC_API_KEY
```

执行：

```bash
unset ANTHROPIC_API_KEY
```

再检查：

```bash
env | grep ANTHROPIC
```

---

## 9.2 模型不存在

如果出现：

```text
模型不存在，请检查模型代码
```

常见原因是：

```text
Claude Code 请求的模型名
        ↓
CLIProxyAPI 模型映射
        ↓
上游 OpenAI / ChatGPT 支持的真实模型名
```

三者没有对上。

排查顺序：

```text
1. 检查 Claude Code 当前使用的模型
2. 检查 CLIProxyAPI 的模型映射配置
3. 检查上游 Provider 实际支持的模型名
4. 修改为真实存在的模型 ID
```

---

## 9.3 CLIProxyAPI 端口不通

检查服务是否启动：

```bash
ss -lntp | grep 8317
```

检查地址是否正确：

```bash
echo $ANTHROPIC_BASE_URL
```

测试连接：

```bash
curl http://127.0.0.1:8317
```

如果 CLIProxyAPI 跑在 Windows，而 Claude Code 跑在 WSL，需要注意：

```text
WSL 中的 127.0.0.1 不一定等于 Windows 宿主机服务
```

这种情况下可以尝试获取 Windows 宿主机 IP：

```bash
cat /etc/resolv.conf | grep nameserver
```

然后将：

```bash
ANTHROPIC_BASE_URL="http://127.0.0.1:8317"
```

改成：

```bash
ANTHROPIC_BASE_URL="http://Windows宿主机IP:8317"
```

---

## 9.4 settings.json 覆盖环境变量

Claude Code 可能从多个位置读取配置，例如：

```text
~/.claude/settings.json
.claude/settings.json
.claude/settings.local.json
shell 环境变量
```

如果配置混乱，建议逐个检查：

```bash
env | grep ANTHROPIC
```

```bash
cat ~/.claude/settings.json
```

```bash
cat .claude/settings.json
```

```bash
cat .claude/settings.local.json
```

如果你之前配置过：

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://open.bigmodel.cn/api/anthropic",
    "ANTHROPIC_AUTH_TOKEN": "xxx"
  }
}
```

那么 Claude Code 可能仍然走旧的 GLM / 智谱中转地址，而不是 CLIProxyAPI。

---

## 9.5 请求超时

如果请求经常超时，可以增加：

```bash
export API_TIMEOUT_MS="3000000"
```

也可以检查：

```text
网络代理
CLIProxyAPI 日志
上游模型服务状态
模型响应速度
本地防火墙
```

---

## 9.6 VS Code 中识别到 Windows Python

如果在 VS Code 中发现 Python 解释器是 Windows 的，例如：

```text
C:\Users\xxx\AppData\Local\Programs\Python\Pythonxxx\python.exe
```

说明你可能没有正确进入 WSL Remote 模式。

解决方法：

1. 在 WSL 终端中进入项目目录。
2. 执行：

```bash
code .
```

3. 确认 VS Code 左下角显示：

```text
WSL: Ubuntu
```

4. 重新选择 Python 解释器：

```text
Ctrl + Shift + P
        ↓
Python: Select Interpreter
        ↓
选择 WSL 中的 Python
```

---

# 10. 推荐目录结构

建议将本文档和流程图放在项目中：

```text
your_project/
├── README.md
├── docs/
│   └── WSL_ClaudeCode_CLIProxyAPI.md
└── assets/
    └── wsl_and_claude_code_workflow_chart.png
```

如果本文档就是主 README，则可以写成：

```text
your_project/
├── README.md
└── assets/
    └── wsl_and_claude_code_workflow_chart.png
```

---

# 11. 最小可用配置总结

如果你已经安装好了所有工具，最小测试流程如下：

```bash
# 1. 进入 WSL
wsl
```

```bash
# 2. 启动 CLIProxyAPI
cli-proxy-api start
```

```bash
# 3. 配置 Claude Code 指向 CLIProxyAPI
export ANTHROPIC_BASE_URL="http://127.0.0.1:8317"
export ANTHROPIC_AUTH_TOKEN="your_proxy_token"
export API_TIMEOUT_MS="3000000"
```

```bash
# 4. 检查变量
env | grep ANTHROPIC
```

```bash
# 5. 启动 Claude Code
claude
```

然后在 Claude Code 中输入：

```text
请帮我分析当前项目结构。
```

如果能正常返回结果，说明流程已经打通。

---

# 12. 最终链路总结

最终链路可以简化理解为：

```text
Windows
  ↓
WSL Ubuntu
  ↓
Claude Code
  ↓
ANTHROPIC_BASE_URL 指向 CLIProxyAPI
  ↓
CLIProxyAPI 完成认证与模型映射
  ↓
ChatGPT / OpenAI / GPT 模型
  ↓
返回结果给 Claude Code
```

核心思想是：

```text
Claude Code 负责终端交互和代码工具调用
CLIProxyAPI 负责 API 协议兼容、认证和模型路由
ChatGPT / GPT 模型负责实际推理和生成结果
```

---

# 13. 参考链接

- Claude Code 环境变量文档：<https://code.claude.com/docs/en/env-vars>
- Claude Code settings 配置文档：<https://code.claude.com/docs/en/settings>
- CLIProxyAPI 中文 README：<https://github.com/router-for-me/CLIProxyAPI/blob/main/README_CN.md>
- CLIProxyAPI GitHub 仓库：<https://github.com/router-for-me/CLIProxyAPI>
