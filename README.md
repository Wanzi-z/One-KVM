<div align="center">
  <img src="https://github.com/mofeng-git/Build-Armbian/assets/62919083/add9743a-0987-4e8a-b2cb-62121f236582" alt="One-KVM Logo" width="300">
  <h1>One-KVM</h1>
  <p><strong>基于 PiKVM 的 DIY IP-KVM 解决方案</strong></p>
  
  <p><a href="README.md">简体中文</a> | <a href="README.en.md">English</a></p>
  
  [![GitHub stars](https://img.shields.io/github/stars/mofeng-git/One-KVM?style=social)](https://github.com/mofeng-git/One-KVM/stargazers)
  [![GitHub forks](https://img.shields.io/github/forks/mofeng-git/One-KVM?style=social)](https://github.com/mofeng-git/One-KVM/network/members)
  [![GitHub issues](https://img.shields.io/github/issues/mofeng-git/One-KVM)](https://github.com/mofeng-git/One-KVM/issues)
  [![GitHub license](https://img.shields.io/github/license/mofeng-git/One-KVM)](https://github.com/mofeng-git/One-KVM/blob/master/LICENSE)
  
  <p>
    <a href="https://docs.one-kvm.cn">📖 详细文档</a> •
    <a href="https://demo.one-kvm.cn/">🚀 在线演示</a> •
    <a href="#快速开始">⚡ 快速开始</a> •
    <a href="#功能介绍">📊 功能介绍</a>
  </p>
</div>

---

## 📋 目录

- [项目概述](#项目概述)
- [功能介绍](#功能介绍)
- [快速开始](#快速开始)
- [贡献指南](#贡献指南)
- [其他](#其他)

## 📖 项目概述

**One-KVM** 是基于开源 [PiKVM](https://github.com/pikvm/pikvm) 项目进行二次开发的 DIY IP-KVM 解决方案。该方案利用成本较低的硬件设备，实现 BIOS 级别的远程服务器或工作站管理功能。

### 应用场景

- **家庭实验室主机管理** - 远程管理服务器和开发设备
- **服务器远程维护** - 无需物理接触即可进行系统维护
- **系统故障处理** - 远程解决系统启动和 BIOS 相关问题

![One-KVM 界面截图](https://github.com/user-attachments/assets/a7848bca-e43c-434e-b812-27a45fad7910)

## 📊 功能介绍

### 核心特性

| 特性 | 描述 | 优势 |
|------|------|------|
| **无侵入性** | 无需在目标机器上安装软件或驱动 | 不依赖操作系统，可访问 BIOS/UEFI 设置 |
| **成本效益** | 利用常见硬件设备（如电视盒子、开发板等） | 降低 KVM over IP 的实现成本 |
| **功能扩展** | 在 PiKVM 基础上增加实用功能 | Docker 部署、视频录制、中文界面 |
| **部署方式** | 支持 Docker 部署和硬件整合包 | 为特定硬件平台提供预配置方案 |

### 项目限制

本项目为个人维护的开源项目，资源有限，无商业运营计划

- 不提供内置免费内网穿透服务，相关问题请自行解决
- 不提供24×7小时技术支持服务
- 不承诺系统稳定性和合规性，使用风险需自行承担
- 尽力优化用户体验，但仍需要一定的技术基础

### 功能对比

> 💡 **说明：** 以下表格展示了 One-KVM 与其他基于 PiKVM 项目的功能对比，仅供参考。如有遗漏或错误，欢迎联系更正。

| 功能特性 | One-KVM | PiKVM | ArmKVM | BLIKVM |
|:--------:|:-------:|:-----:|:------:|:------:|
| 简体中文 WebUI | ✅ | ❌ | ✅ | ✅ |
| 远程视频流 | MJPEG/H.264 | MJPEG/H.264 | MJPEG/H.264 | MJPEG/H.264 |
| H.264 视频编码 | CPU/GPU | GPU | 未知 | GPU |
| 远程音频流 | ✅ | ✅ | ✅ | ✅ |
| 远程鼠键控制 | OTG/CH9329 | OTG/CH9329/Pico/Bluetooth | OTG | OTG |
| VNC 控制 | ✅ | ✅ | ✅ | ✅ |
| ATX 电源控制 | GPIO/USB 继电器 | GPIO | GPIO | GPIO |
| 虚拟存储驱动器挂载 | ✅ | ✅ | ✅ | ✅ |
| 网页终端 | ✅ | ✅ | ✅ | ✅ |
| Docker 部署 | ✅ | ❌ | ❌ | ❌ |
| 商业化运营 | ❌ | ✅ | ✅ | ✅ |

## ⚡ 快速开始

### 方式一：Docker 镜像部署（推荐）

Docker 版本支持 OTG 或 CH9329 作为虚拟 HID，兼容 amd64、arm64、armv7 架构的 Linux 系统。

#### 一键脚本部署

```bash
curl -sSL https://docs.one-kvm.cn/quick_start.sh -o quick_start.sh && bash quick_start.sh
```

#### 手动部署

推荐使用 --net=host 网络模式以获得更好的 wol 功能和 webrtc 通信支持。

docker host 网络模式：

    端口 8080：HTTP Web 服务
    端口 4430：HTTPS Web 服务
    端口 5900：VNC 服务
    端口 623：IPMI 服务
    端口 20000-40000：WebRTC 通信端口范围，用于低延迟视频传输
    端口 9（UDP）：Wake-on-LAN（WOL）唤醒功能

docker host 模式：

**使用 OTG 作为虚拟 HID：**

```bash
sudo docker run --name kvmd -itd --privileged=true \
    -v /lib/modules:/lib/modules:ro -v /dev:/dev \
    -v /sys/kernel/config:/sys/kernel/config -e OTG=1 \
    --net=host \
    silentwind0/kvmd
```

**使用 CH9329 作为虚拟 HID：**

```bash
sudo docker run --name kvmd -itd \
    --device /dev/video0:/dev/video0 \
    --device /dev/ttyUSB0:/dev/ttyUSB0 \
    --net=host \
    silentwind0/kvmd
```

docker bridge 模式：

**使用 OTG 作为虚拟 HID：**

```bash
sudo docker run --name kvmd -itd --privileged=true \
    -v /lib/modules:/lib/modules:ro -v /dev:/dev \
    -v /sys/kernel/config:/sys/kernel/config -e OTG=1 \
    -p 8080:8080 -p 4430:4430 -p 5900:5900 -p 623:623 \
    silentwind0/kvmd
```

**使用 CH9329 作为虚拟 HID：**

```bash
sudo docker run --name kvmd -itd \
    --device /dev/video0:/dev/video0 \
    --device /dev/ttyUSB0:/dev/ttyUSB0 \
    -p 8080:8080 -p 4430:4430 -p 5900:5900 -p 623:623 \
    silentwind0/kvmd
```

### 方式二：直刷 One-KVM 整合包

针对特定硬件平台，提供了预配置的 One-KVM 打包镜像，简化部署流程，实现开箱即用。

#### 固件下载

**GitHub 下载：**
- **GitHub Releases：** [https://github.com/mofeng-git/One-KVM/releases](https://github.com/mofeng-git/One-KVM/releases)

**其他下载方式：**
- **免登录高速下载：** [http://sd1.files.one-kvm.cn/](http://sd1.files.one-kvm.cn/)（由群友赞助，支持直链，接入 EdgeOne CDN，建议使用多线程下载工具下载获取最高速度）
- **免登录下载：** [https://pan.huang1111.cn/s/mxkx3T1](https://pan.huang1111.cn/s/mxkx3T1) （由 Huang1111公益计划 提供）
- **百度网盘：** [https://pan.baidu.com/s/166-2Y8PBF4SbHXFkGmFJYg?pwd=o9aj](https://pan.baidu.com/s/166-2Y8PBF4SbHXFkGmFJYg?pwd=o9aj) （提取码：o9aj）

#### 支持的硬件平台

| 固件型号 | 固件代号 | 硬件配置 | 最新版本 | 状态 |
|:--------:|:--------:|:--------:|:--------:|:----:|
| 玩客云 | Onecloud | USB 采集卡、OTG | 241018 | ✅ |
| 私家云二代 | Cumebox2 | USB 采集卡、OTG | 241004 | ✅ |
| Vmare | Vmare-uefi | USB 采集卡、CH9329 | 241004 | ✅ |
| Virtualbox | Virtualbox-uefi | USB 采集卡、CH9329 | 241004 | ✅ |
| s905l3a 通用包 | E900v22c | USB 采集卡、OTG | 241004 | ✅ |
| 我家云 | Chainedbox | USB 采集卡、OTG | 241004 | ✅ |
| 龙芯久久派 | 2k0300 | USB 采集卡、CH9329 | 241025 | ❌ |

### 报告问题

如果您发现了问题，请：
1. 使用 [GitHub Issues](https://github.com/mofeng-git/One-KVM/issues) 报告
2. 提供详细的错误信息和复现步骤
3. 包含您的硬件配置和系统信息

### 赞助支持

本项目基于多个优秀开源项目进行二次开发，作者投入了大量时间进行测试和维护。如果您觉得这个项目有价值，欢迎通过 **[为爱发电](https://afdian.com/a/silentwind)** 支持项目发展。

#### 感谢名单

<details>
<summary><strong>点击查看感谢名单</strong></summary>

- 浩龙的电子嵌入式之路

- Tsuki

- H_xiaoming

- 0蓝蓝0

- fairybl

- Will

- 浩龙的电子嵌入式之路

- 自.知

- 观棋不语٩ ི۶

- 爱发电用户_a57a4

- 爱发电用户_2c769

- 霜序

- 远方（闲鱼用户名：小远技术店铺）

- 爱发电用户_399fc

- 斐斐の

- 爱发电用户_09451

- 超高校级的錆鱼

- 爱发电用户_08cff

- guoke

- mgt

- 姜沢掵

- ui_beam

- 爱发电用户_c0dd7

- 爱发电用户_dnjK

- 忍者胖猪

- 永遠の願い

- 爱发电用户_GBrF

- 爱发电用户_fd65c

- 爱发电用户_vhNa

- 爱发电用户_Xu6S

- moss

- woshididi

- 爱发电用户_a0fd1

- 爱发电用户_f6bH

- 码农

- 爱发电用户_6639f

- jeron

- 爱发电用户_CN7y

- 爱发电用户_Up6w

- 爱发电用户_e3202

- 一语念白

- 云边

- 爱发电用户_5a711

- 爱发电用户_9a706

- T0m9ir1SUKI

- 爱发电用户_56d52

- 爱发电用户_3N6F

- DUSK

- 飘零

- .

- 饭太稀

- 葱

- ......

</details>

#### 赞助商

本项目得到以下赞助商的支持：

**CDN 加速及安全防护：**
- **[Tencent EdgeOne](https://edgeone.ai/zh?from=github)** - 提供 CDN 加速及安全防护服务

![Tencent EdgeOne](https://edgeone.ai/media/34fe3a45-492d-4ea4-ae5d-ea1087ca7b4b.png)

**文件存储服务：**
- **[Huang1111公益计划](https://pan.huang1111.cn/s/mxkx3T1)** - 提供免登录下载服务

**云服务商**

- **[林枫云](https://www.dkdun.cn)** - 赞助了本项目宁波大带宽服务器

![林枫云](./img/36076FEFF0898A80EBD5756D28F4076C.png)

林枫云主营国内外地域的精品线路业务服务器、高主频游戏服务器和大带宽服务器。

## 📚 其他

### 使用的开源项目

本项目基于以下优秀开源项目进行二次开发：

- [PiKVM](https://github.com/pikvm/pikvm) - 开源的 DIY IP-KVM 解决方案