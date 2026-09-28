# 解决 Codex CLI 频繁断连与重连（Reconnecting）问题指南

在使用 Codex CLI 过程中，若经常遇到长连接中断、报错 `Stream disconnected before completion: Transport error: network error: error decoding response body` 或频繁触发 `Reconnecting...`，通常是由于本地代理（如 Clash）对长连接 WebSocket 支持不稳定、空闲超时切断或终端未正确读取代理导致的。

本文档汇总了三种行之有效的解决方案，建议按顺序优先尝试 **方法一**。

---

## 解决方案一：禁用 WebSocket，强制回退至纯 HTTP 流（推荐）

该方法通过配置将底层的双向长连接 WebSocket 降级为标准的 HTTPS 块传输流（HTTP/SSE），对代理节点和中转协议的兼容性最高，能彻底解决因 WS 心跳超时导致的断连。

### 1. 定位配置文件 `config.toml`

* **Linux / macOS**: `~/.codex/config.toml`
* **Windows**: `C:\Users\<你的用户名>\.codex\config.toml`

### 2. 修改配置

用文本编辑器打开上述文件：

* 在文件**顶部**添加：
```toml
model_provider = "openai_http"

```


* 在文件**底部**添加：
```toml
[model_providers.openai_http]
name = "OpenAI HTTP only"
wire_api = "responses"
supports_websockets = false

```


### 3. 重启 Codex

保存文件后，完全退出当前的 Codex 终端进程并重新启动会话。

---

## 解决方案二：配置 `.env` 环境变量显式注入代理

如果希望保留 WebSocket 机制，但终端经常无法正确继承系统或终端的环境变量，可通过 Codex 专用的 `.env` 文件进行强制代理绑定。

### 1. 定位或新建 `.env` 文件

* **Linux / macOS**: `~/.codex/.env`
* **Windows**: `C:\Users\<你的用户名>\.codex\.env`

### 2. 写入代理配置

将以下内容写入该文件（此处以常见代理客户端默认端口 `7890` 为例，根据实际端口调整）：

```env
HTTP_PROXY="http://127.0.0.1:7890"
HTTPS_PROXY="http://127.0.0.1:7890"
NO_PROXY="localhost,127.0.0.1,::1"

```

> **注意**：如果依然遇到 SSL 握手断开，可尝试将协议前缀改为 SOCKS5：
> ```env
> HTTP_PROXY="socks5h://127.0.0.1:7890"
> HTTPS_PROXY="socks5h://127.0.0.1:7890"
> 
> ```
> 
> 

---

## 解决方案三：开启代理客户端 TUN 虚拟网卡模式（全局备用方案）

若上述应用层配置均未生效，或在 Windows / WSL 混合环境下存在跨网络栈通信问题，可直接使用网卡级接管。

### 操作步骤

1. 打开本地代理客户端（如 Clash Verge、Clash for Windows 等）。
2. 在设置中开启 **TUN 模式（TUN Mode）**。
* *注：Windows 环境下通常需先安装虚拟网卡服务（Service Mode / Wintun 驱动）。*

3. 确保出站规则或全局规则处于有效代理节点。
4. TUN 模式下系统底层所有 TCP/UDP 流量将由虚拟网卡无缝劫持接管，无需在 Codex 内部做任何繁琐的环境变量设置。

---
