# NexusGate

<h1 align="center">请勿用于商业用途!!!</h1>

基于 OpenWrt 24.10 的定制固件，专注于 x86 平台和 Tailscale 网络优化。NexusGate 旨在提供一个高性能、易用的网络连接解决方案。

## 特性

- 基于原生 OpenWrt 24.10 编译，默认管理地址 10.11.12.1
- 专注于 x86 平台优化
  - 使用 x86-64-v2 指令集优化
  - 默认关闭 CPU 漏洞缓解以提升性能
  - 针对 Intel 网卡进行优化

- 网络性能优化
  - 启用 BBRv3 拥塞控制
  - 启用 SFE 流量加速
  - 启用 Flow Offload 硬件转发
  - 优化 UDP 和 TCP 性能

- Tailscale 集成优化
  - 首次启动自动安装配置 Tailscale
  - 专用防火墙区域配置
  - 完整的内外网访问支持
  - 支持通过代理软件（如 Mihomo）转发流量
  - 优化的 DNS 和 TPROXY 配置

- 预配置功能
  - 内置 Tailscale 一键安装脚本
  - 安卓 TV 联网优化
  - 完整的 IPv6 支持
  - 支持 Full-cone NAT
  - 预装代理软件
    * Mihomo (原 Clash Meta)：全功能代理内核
    * DAED：轻量级代理客户端

## 使用说明

1. 首次启动
   - 系统将自动配置网络（IP: 10.11.12.1）
   - 自动安装并配置 Tailscale
   - 自动优化系统参数

2. Tailscale 配置
   - 执行 `tailscale up` 进行认证
   - 防火墙已预配置好 Tailscale 区域
   - 支持与其他代理软件协同工作

3. 性能优化
   - 已启用 BBRv3 和 SFE
   - 已优化网络缓冲区和连接跟踪参数
   - 已配置硬件加速和流量卸载

## 注意事项

- 本固件仅支持 x86 平台
- 建议使用支持硬件加速的网卡
- 如遇问题，可执行 `fuck` 命令重置网络

## 鸣谢

- [QiuSimons/YAOF](https://github.com/QiuSimons/YAOF) - 原始项目灵感来源
- [OpenWrt](https://github.com/openwrt/openwrt) - 主要上游项目
- [Tailscale](https://github.com/tailscale/tailscale) - VPN 核心组件
- [CH3NGYZ/tailscale-openwrt](https://github.com/CH3NGYZ/tailscale-openwrt) - Tailscale OpenWrt 安装脚本
- [ImmortalWrt](https://github.com/immortalwrt/immortalwrt) - 部分补丁和功能来源

## 构建说明

### 目录结构
```
.
├── PATCH/                # 补丁和自定义文件
│   ├── files/           # 预置文件，会自动复制到固件中
│   │   ├── etc/        # 系统配置文件
│   │   └── usr/        # 用户程序和脚本
│   └── pkgs/           # 软件包补丁
├── SCRIPTS/            # 编译脚本
│   ├── X86/           # x86 平台特定脚本
│   └── *.sh           # 通用编译脚本
└── SEED/              # 配置文件
    └── X86/           # x86 平台配置
```

### 构建流程
1. 初始化环境
   ```bash
   ./SCRIPTS/01_get_ready.sh      # 克隆必要的源码
   ./SCRIPTS/02_prepare_package.sh # 准备软件包
   ```

2. 应用补丁和配置
   - 系统补丁位于 `PATCH/` 目录
   - 预置文件位于 `PATCH/files/` 目录
   - 平台配置位于 `SEED/X86/` 目录

3. 编译固件
   ```bash
   make menuconfig      # 可选：调整配置
   make download -j8    # 下载软件包
   make V=s -j$(nproc) # 开始编译
   ```

### 自定义修改指南

1. 修改网络配置
   - 编辑 `PATCH/files/etc/config/network`
   - 或修改 `PATCH/files/etc/uci-defaults/99-custom-network`

2. 修改防火墙规则
   - 编辑 `PATCH/files/etc/config/firewall`
   - 添加自定义防火墙规则

3. 添加软件包
   - 在 `SEED/X86/config.seed` 中添加需要的包
   - 或在 menuconfig 中选择

4. 修改系统参数
   - 编辑 `PATCH/files/etc/sysctl.d/` 下的配置文件
   - 修改 `SCRIPTS/02_prepare_package.sh` 中的优化参数

5. 配置代理软件
   - Mihomo：支持 TPROXY 和 TUN 模式，推荐作为主要代理工具
   - DAED：支持 VLESS/VMess/Trojan，轻量且高效
   - 添加其他代理软件：
     * 编辑 `SEED/X86/config.seed` 文件
     * 取消注释或添加以下行：
       ```
       CONFIG_PACKAGE_luci-app-ssr-plus=y    # 添加 SSR Plus+
       CONFIG_PACKAGE_luci-app-openclash=y   # 添加 OpenClash
       ```

6. 添加自定义功能
   - 在 `PATCH/files/` 添加所需文件
   - 在 `PATCH/files/etc/uci-defaults/` 添加初始化脚本

### 常见问题

1. 编译环境
   - 推荐使用 Ubuntu 20.04 或更新版本
   - 需要安装基本编译环境和依赖

2. 空间要求
   - 至少 50GB 可用空间
   - 建议使用 SSD 以加快编译速度

3. 内存要求
   - 至少 8GB RAM
   - 建议使用 16GB 或更多

## License

本项目基于 GPL-3.0 开源协议。严禁用于商业用途。