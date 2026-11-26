title: linux
author: John Doe
date: 2026-11-02 17:41:01
tags:
---
# Ubuntu 24.04 LTS（WSL2）使用小抄

> 你的 Ubuntu 子系统已安装在 `F:\爱吃狄大人\ubuntu`（WSL2，版本 24.04.5 LTS）
> 日常登录用户：`wangzutao`（sudo 免密）｜ systemd 已启用
> 整理日期：2026-11-02

---

## 一、进出 Ubuntu

| 操作 | 命令 |
|------|------|
| 进入 Ubuntu（当前默认发行版） | `wsl` |
| 进入指定发行版 | `wsl -d Ubuntu-24.04` |
| 以 root 身份进入 | `wsl -d Ubuntu-24.04 -u root` |
| 退出 | 输入 `exit`，或按 `Ctrl+D` |
| 从 Ubuntu 里回到 Windows | `exit` |

> 建议把 **Windows Terminal** 设为默认终端（微软商店免费），体验更好。

---

## 二、常用 WSL 管理命令（在 Windows PowerShell / 终端里执行）

| 目的 | 命令 |
|------|------|
| 查看已装发行版及默认项 | `wsl --list --verbose`（默认带 `*`） |
| 查看可安装的发行版 | `wsl --list --online` |
| 设置默认发行版 | `wsl --set-default Ubuntu-24.04` |
| 彻底关闭所有 WSL（释放内存） | `wsl --shutdown` |
| 查看 WSL 版本 | `wsl --version` |
| 更新 WSL | `wsl --update` |

---

## 三、Windows 与 Ubuntu 之间互相访问文件

- **在 Ubuntu 里访问 Windows 文件**：Windows 磁盘挂载在 `/mnt` 下
  ```
  cd /mnt/c            # C 盘
  cd /mnt/f/爱吃狄大人/ubuntu   # 访问 F 盘（注意中文路径）
  ```
- **在 Windows 里访问 Ubuntu 的文件**：用资源管理器地址栏输入
  ```
  \\wsl$\Ubuntu-24.04\home\wangzutao
  ```
  或运行 `explorer.exe .`（在 Ubuntu 里打开当前目录的 Windows 资源管理器窗口）。

---

## 四、用户与权限

| 目的 | 命令 |
|------|------|
| 修改自己的登录密码 | `passwd` |
| 临时提权 | `sudo <命令>`（如 `sudo apt update`） |
| 进入 root shell | `sudo -i` |
| 修改默认登录用户 | 编辑 `/etc/wsl.conf` 的 `[user]` 段，改完后 `wsl --shutdown` 再进 |

> 当前 sudo 已配置为免密（`/etc/sudoers.d/wangzutao`），日常开发很方便；如需安全起见可去掉 `NOPASSWD`。

---

## 五、软件包管理（apt）

| 目的 | 命令 |
|------|------|
| 更新软件源索引 | `sudo apt update` |
| 升级已装软件 | `sudo apt upgrade -y` |
| 安装软件 | `sudo apt install -y <包名>` |
| 搜索软件 | `apt search <关键词>` |
| 卸载软件 | `sudo apt remove <包名>` |
| 清理 | `sudo apt autoremove` |

---

## 六、systemd 服务（已启用）

| 目的 | 命令 |
|------|------|
| 查看服务状态 | `systemctl status <服务>` |
| 开机自启 | `sudo systemctl enable <服务>` |
| 启动/停止 | `sudo systemctl start / stop <服务>` |
| 查看失败的单元 | `systemctl --failed` |

> 目前仅 `systemd-binfmt` 一个服务失败，属 WSL2 已知无害现象，可忽略。

---

## 七、常见开发环境速装（按需）

```bash
# 编译工具链
sudo apt install -y build-essential

# Git
sudo apt install -y git
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"

# Python3
sudo apt install -y python3 python3-pip

# Node.js（用官方源更稳妥，这里用 apt 默认版）
sudo apt install -y nodejs npm
```

---

## 八、网络小技巧

- 在 Ubuntu 里查看本机 IP：`ip addr`（WSL2 是独立于 Windows 的虚拟网段）
- Windows 与 Ubuntu 互访：默认可直接互通；如需从局域网访问 Ubuntu 服务，需额外配置端口转发
- 局域网内其他设备访问 Ubuntu 里的服务（如 Web 服务）：WSL2 默认不直接暴露，需 `netsh interface portproxy` 转发，或改用 WSL 的 localhost 转发

---

## 九、常见问题速查

| 现象 | 处理 |
|------|------|
| 输入 `wsl` 报"没有已安装的分发" | 确认是否误卸载，`wsl --install -d Ubuntu-24.04` |
| Ubuntu 卡住/无响应 | `wsl --shutdown` 后重新进入 |
| 磁盘占用大 | `sudo apt autoremove && sudo apt clean` |
| 想整体迁移/备份 | `wsl --export Ubuntu-24.04 备份.tar` → 之后 `wsl --import ...` |

---

*小抄到此，按需查看。需要我补充某一块（比如 Docker、开发环境、网络转发）也可以再细化。*
