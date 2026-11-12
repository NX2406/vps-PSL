# VPS 端口智能化限速脚本

这是一个用于 VPS 的网络流量管理工具。它可以帮助你对指定端口或 Xray 进程进行限速，防止流量滥用，并尽量让实际测速结果接近设定值。

## 功能特点

- 🚀 **简单易用**：一键脚本，交互式菜单
- 🎯 **端口限速**：支持针对指定端口设置入站/出站限速
- 🔢 **小数精度**：速率支持小数点后一位，例如 `10.5 Mbps`
- ⚡ **双向控制**：可分别设置入站 Ingress 和出站 Egress
- 🧠 **更精准策略**：使用 HTB + fq_codel，避免 HTB + TBF 双重限速导致速度明显低于目标
- 🛠️ **一键菜单**：首次运行后自动安装 `PSL` / `psl` 命令，后续直接输入 `PSL` 打开菜单

## 使用方法（Root 用户）

首次安装/运行：

```bash
bash <(curl -sL https://raw.githubusercontent.com/NX2406/vps-PSL/refs/heads/main/speed%20limiting)
```

首次运行后，脚本会自动安装到：

```text
/usr/local/bin/PSL
/usr/local/bin/psl -> /usr/local/bin/PSL
```

以后直接执行：

```bash
PSL
```

或：

```bash
psl
```

即可打开菜单，无需重新执行 curl 安装命令。

## 重启后自动恢复

`tc` 限速规则和 IFB 网卡在重启后都会消失。从 V10.31 起，设置过限速后脚本会自动登记开机恢复：

- 有 systemd 的系统：安装并启用 `psl-restore.service`
- 没有 systemd 的系统：在 root 的 crontab 里加一条 `@reboot` 任务

开机后会按限速清单（`/var/lib/vps-tc/limits.db`）重新下发全部规则。也可以随时手动恢复：

```bash
PSL restore
```

菜单里的「全部重置」和「彻底卸载」会同时移除开机恢复。

## 支持的系统

- Debian / Ubuntu（apt）
- CentOS 7、RHEL / Rocky / AlmaLinux 8 及以上、Fedora（yum / dnf）

缺少的依赖会按命令逐个补装（例如 RHEL 8+ 的 `tc` 在单独的 `iproute-tc` 包里），某个包装不上不会连累其他依赖。

## 限速精度说明

输入速率支持：

```text
0.1
1.5
10
10.5
100.0
```

脚本内部默认会加 5% 链路层补偿，因为 `tc` 控制的是链路层速率，而 speedtest/iperf 等应用层测速会扣除 TCP/IP/隧道头部开销。

例如输入：

```text
100.0 Mbps
```

内部会设置约：

```text
105000 kbit
```

这样应用层测速更容易接近目标值。

## 卸载

菜单中选择：

```text
10. 彻底卸载与清理
```

会清理限速规则、开机恢复、状态目录以及 `/usr/local/bin/PSL` / `/usr/local/bin/psl`。

## 更新记录

### V10.31

- 修复 Debian/Ubuntu 装依赖时用了不存在的包名 `iproute2-tc`，整条安装失败，最小化系统缺命令直接退出
- 修复 RHEL 8+/Fedora 没有安装 `iproute-tc`，缺 `tc` 直接退出
- 依赖改为缺哪个命令装哪个包；补上 `cmp` 所需的 `diffutils`
- 新增开机自动恢复（`psl-restore` 服务 / `@reboot` 任务）和 `PSL restore` 命令
- 修复默认路由没有网关时主网卡被识别成 IP 地址；纯 IPv6 机器也能识别主网卡
- 修复 CentOS 7 的 `ss` 不支持 `-H` 导致端口列表为空
- 修复网卡上已有 fq / cake 等 root 队列时创建 HTB 失败
- 同一端口同一方向重复限速时沿用原来的 class，不再残留旧规则；速率不能为 0
- 菜单改为循环，不再每做一次操作递归一层

### V10.30

- 首次运行自动安装 `PSL` / `psl` 命令
- 速率支持一位小数
- 限速策略改为 HTB + fq_codel，按目标速率动态计算 burst，默认 5% 链路层补偿
