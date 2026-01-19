# VPS 端口智能化限速脚本

这是一个用于 VPS 的网络流量管理工具。它可以帮助你对指定端口进行智能化限速，有效防止流量滥用，合理分配服务器网络资源。

## 功能特点

- 🚀 **简单易用**: 一键脚本，交互式菜单，无需复杂配置
- 🎯 **精准限速**: 支持针对特定端口进行独立的带宽限制
- ⚡ **双向控制**: 可分别设置入站 (Ingress) 和出站 (Egress) 的速度阈值
- 🛠️ **灵活管理**: 支持实时查看当前规则，并可随时清除限速设置

## 使用方法 (Root 用户)

直接在终端执行以下命令即可：

```bash
bash <(curl -sL [https://raw.githubusercontent.com/NX2406/vps-PSL/refs/heads/main/speed%20limiting](https://raw.githubusercontent.com/NX2406/vps-PSL/refs/heads/main/speed%20limiting))
