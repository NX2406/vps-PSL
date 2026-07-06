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
bash <(curl -sL https://cdn.jsdelivr.net/gh/NX2406/vps-PSL@main/speed%20limiting)
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

会清理限速规则、状态目录以及 `/usr/local/bin/PSL` / `/usr/local/bin/psl`。
