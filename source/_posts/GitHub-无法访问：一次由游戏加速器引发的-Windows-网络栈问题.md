---
title: GitHub 无法访问：一次由游戏加速器引发的 Windows 网络栈问题
date: 2026-01-15 19:47:19
tags: 网络问题
---

## 问题现象

- 浏览器无法访问 `https://github.com`
- 提示连接被关闭（`ERR_CONNECTION_CLOSED`）
- 命令行执行：

```
curl https://github.com
```

返回：

```
Failed to connect to github.com port 443
```

但：

- Google 等其他国外网站可正常访问
- 切换网络、开启 VPN、修改 DNS 均无效

------

## 问题原因

**Windows 本机网络栈（Winsock / LSP）被游戏加速器污染。**

在使用 **雷神加速器** 给游戏加速后，其对系统网络的底层修改未完全恢复，导致 GitHub 这类 HTTPS 要求严格的网站连接被异常中断。

问题发生在**本机层面**，因此：

- 换网络无效
- VPN 无效

------

## 解决思路

直接重置 Windows 网络栈，清除第三方残留的网络钩子。

------

## 解决步骤

1. **以管理员身份** 打开 CMD
2. 依次执行：

```
netsh winsock reset
netsh int ip reset
ipconfig /flushdns
```

1. **重启电脑**
2. 重新连接 VPN
3. 访问 GitHub 或执行：

```
curl https://github.com
```

问题解决。

------

## 备注

- 若近期使用过游戏加速器 / 抓包工具 / 代理软件，且出现“只有部分 HTTPS 网站无法访问”，可优先尝试此方案。
- `netsh winsock reset` 是解决 Windows 网络异常的**“保命命令”**。
