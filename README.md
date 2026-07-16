# Netcore N30 Pro OpenWrt 固件构建器

本仓库通过 GitHub Actions 为 Netcore N30 Pro 构建基于 ImmortalWrt 24.10 / Linux 6.6 的 OpenWrt 固件。源码、PassWall2 和相关软件包均固定到指定提交，以提高构建的可复现性。

## 固件功能

- 中文 LuCI 管理界面，仅通过 **HTTP** 提供访问
- PassWall2，仅显式选用 Xray-core，并包含 v2ray-geodata
- DDNS、UPnP、Samba4、ttyd、网络唤醒和流量统计
- USB 3.0 存储、UAS、EXT4 文件系统和自动挂载
- ZRAM 压缩交换空间
- 四个 LAN 口固定映射为 `lan1`～`lan4`，独立 WAN 使用 `eth1`
- 从 Factory 分区生成稳定且互不重复的 2.4 GHz、5 GHz Wi-Fi MAC 地址
- 2.4 GHz 和 5 GHz 无线默认使用中国区域，发射功率设置为 26 dBm；实际功率仍受驱动、校准数据、信道和法规限制
- 关闭软件包仓库签名检查，允许安装本地构建或第三方软件包；HTTPS 传输和源码归档哈希检查仍然保留

## 默认配置

| 项目 | 默认值 |
| --- | --- |
| LAN 地址 | `192.168.6.1` |
| LuCI 地址 | `http://192.168.6.1/` |
| LuCI HTTPS | 已禁用 |
| 用户名 | `root` |
| 初始密码 | 空 |
| 无线区域 | `CN` |
| 无线发射功率 | `26 dBm` |

首次登录后请立即为 `root` 设置强密码。由于固件允许安装未签名软件包，请只添加可信的软件源并核对软件包来源。

## 构建方法

向 `main` 分支推送提交会自动触发构建，也可以在仓库中打开 **Actions → Build Netcore N30 Pro firmware → Run workflow** 手动运行。

工作流会完成以下操作：

1. 检出固定版本的 ImmortalWrt 源码及 PassWall2 软件包。
2. 加入 N30 Pro 的 DTS、镜像定义、网络端口映射、无线 MAC 修复和首次启动设置。
3. 应用 binutils 在 musl libc 环境下的兼容补丁，并补充固定源码版本缺失的 MediaTek HNAT 通知常量。
4. 校验关键设备配置和必需软件包，然后编译固件。
5. 将结果保存为 GitHub Actions 产物，并发布到 GitHub Release。

工作流会缓存源码下载、主机构建工具、目标工具链和 ccache 编译结果。首次构建需要初始化缓存，后续使用相同源码版本和配置的构建可以复用缓存。

## 固件产物

刷机时请选择文件名包含 `netcore_n30pro` 的 `sysupgrade.itb` 镜像。工作流还会生成 Bootloader 相关文件，但除非已经单独确认 Bootloader、闪存和内存布局完全匹配，否则不要刷入这些文件。

## 设备支持来源

- DTS 和镜像定义基于 [huladabang/openwrt-n30pro](https://github.com/huladabang/openwrt-n30pro)。
- 构建源码来自 [padavanonly/immortalwrt-mt798x-6.6](https://github.com/padavanonly/immortalwrt-mt798x-6.6) 的 `openwrt-24.10-6.6` 分支。
- 目标硬件为 MT7981B、512 MB 内存和 128 MB SPI-NAND。

## 刷机警告

只能通过兼容的 Bootloader 或现有的兼容 OpenWrt 系统刷入 `sysupgrade.itb`。刷机存在设备变砖和配置丢失风险，请事先备份配置及 Factory 分区，并确认设备型号和分区布局无误。
