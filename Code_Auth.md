# Codex Auth 使用指南

用于管理和切换 Codex 账号的 CLI 工具配置与操作说明。

---

## 1. 安装

通过 npm 全局安装工具包：

```bash
npm install -g @loongphy/codex-auth

```

---

## 2. 环境变量配置

如果安装后直接运行 `codex-auth` 提示找不到命令，需将全局 npm 路径加入环境变量。

### 2.1 检查安装路径

```bash
type -p codex-auth
# 输出示例：/home/<your_username>/.npm-global/bin/codex-auth

```

### 2.2 写入环境变量

* **Bash 用户：**
```bash
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

```


* **Zsh 用户：**
```bash
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

```



---

## 3. 常用命令

配置好环境变量后，可在任意终端路径下直接使用以下命令：

### 3.1 添加账号（设备授权）

```bash
codex-auth login --device-auth

```

### 3.2 查看已有账号列表

```bash
codex-auth list

```

### 3.3 交互式切换账号

```bash
codex-auth switch

```
