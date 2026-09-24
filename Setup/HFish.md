# Setup HFish HoneyPot
___
## 项目简介
  - 基于 OCI 的云蜜罐实验环境
  - 使用 HFish 收集互联网攻击流量
  - 分析自动化扫描、爆破、恶意连接行为
  - 学习云安全与主机加固
___
## 项目目的
 - 学习搭建蜜罐
 - 观察真实互联网攻击
 - 研究攻击者行为
 - 为未来的IDS做流量收集
 - 用家里机器怕被打穿+运行商封号
___
## 为何选择HFish
  ### 常见蜜罐方案对比
    | 方案         | 原因                    |
    | ---------- | ------------------       |
    | HFish      | 轻量、中文生态好、支持多协议  |
    | Cowrie     | SSH/Telnet 强，但功能单一   |
    | T-Pot      | 太重，OCI 免费机型可能跑不动  |
    | Dionaea    | 偏恶意软件收集              |
    | OpenCanary | 更适合轻量告警              |
  ### 选择HFish原因：
    - 我关注的B站UP·主网络小白_Uncle城·用它
    - 支持协议全面
    - 有中文教程，适合入门
    - 未来会考虑增加或更换蜜罐
___
## 结构
```text
  Internet
     ↓
  OCI Public IP
     ↓
  VCN
     ↓
  Public Subnet
     ↓
  Ubuntu
     ├── HFish
     │     
     └── UFW/iptables
```
---
## 过程
1. 在本实例所在的 Oracle Virtual Cloud Network 放行 4433 端口
```
Source Type: CIDR
Source CIDR: 我的公网络IP/32
IP Protocol: TCP
Source Port Range: 留空
Destination Port Range: 4433
Description: HFish Management
```

2. 操作系统开放 4433 端口作为管理页面
进入iptable在INPUT REJECT之前添加一条ACCEPT 4433
```
sudo nano /etc/iptables/rules.v4
```
```
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 4433 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
```
3. 关闭 111 端口以减少暴露面
https://docs.oracle.com/en-us/iaas/Content/File/Troubleshooting/check-mt-network-rpcinfo.htm
根据甲骨文官方的说明，该端口适用于 OCI File Storage / NFS Mount Target 的连通性检查。我的服务器没有挂载 OCI File Storge，因此可以关闭该端口和 `rpcbind` 进程。

关闭进程：
```
sudo systemctl disable --now rpcbind.socket rpcbind.service
```
检查：
```
ss -lntup
```


___
## 技术栈
  - Oracle Cloud Infrastructure (OCI)
  - Ubuntu Server 24
  - Docker / Docker Compose
  - HFish
  - UFW / iptables
