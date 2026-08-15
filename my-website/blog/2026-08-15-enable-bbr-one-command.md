---
slug: enable-bbr-one-command
title: 一条命令开启 BBR 加速
authors: xiaolinbenben
tags: [BBR, VPS, 网络优化]
---

VPS 速度慢，多半是没开 BBR。BBR 是 Google 出的拥塞控制算法，对高延迟、丢包的线路提升很明显，国内访问国外服务器尤其值得开。

开 BBR 不用换机器、不用加钱，一条命令就行。

<!-- truncate -->

## 一键脚本

[adsorgcn/bbr-script](https://github.com/adsorgcn/bbr-script) 是一个 BBR 一键加速脚本，支持 CentOS / Ubuntu / Debian，自动检测依赖、自动修复 CentOS 停服源，国内 VPS 自动切阿里云镜像。在服务器上执行：

```bash
bash <(curl -sL https://raw.githubusercontent.com/adsorgcn/bbr-script/main/newbbr.sh)
```

或者用 wget：

```bash
wget -O newbbr.sh https://raw.githubusercontent.com/adsorgcn/bbr-script/main/newbbr.sh && bash newbbr.sh
```

## 跑起来很简单

脚本会先检查系统、内核、虚拟化类型，然后弹出菜单。新手直接选 `1` 安装/启用 BBR。

- 内核支持 BBR（4.9+）：**30 秒搞定**
- 内核太老：选 `2`（Ubuntu/Debian）或 `3`（CentOS）升级内核，重启后再跑一次选 `1`
- `4` 清理旧内核，`5` 查看状态

## 怎么确认生效了

验证两行：

```bash
lsmod | grep bbr
sysctl net.ipv4.tcp_congestion_control
```

能看到 `tcp_bbr` 和 `bbr`，就成了。

## 注意：OpenVZ 不行

BBR 要换内核，OpenVZ 换不了。先检查：

```bash
systemd-detect-virt
```

输出 `openvz` 就没辙，换台 KVM 的机器吧。

脚本会保留旧内核，出问题可以回退，但建议还是先备份再操作。跑完能快多少，就看你的线路了。
