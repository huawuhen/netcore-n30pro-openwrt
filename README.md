# Netcore N30 Pro OpenWrt 固件构建器

本仓库通过 GitHub Actions 构建 Netcore N30 Pro 的 OpenWrt 固件。

## 固件功能

- 支持中文界面的 LuCI，仅通过 **HTTP** 提供访问
- PassWall2、Xray-core 和 v2ray-geodata
- DDNS、UPnP、Samba4、ttyd、网络唤醒、流量统计和 AdGuard Home
- USB 调制解调器及常用 USB 网络设备驱动

初始设备设置：

| 项目 | 默认值 |
| --- | --- |
| LAN 地址 | `192.168.6.1` |
| LuCI 地址 | `http://192.168.6.1/` |
| LuCI HTTPS | 已禁用 |
| 用户名 | `root` |
| 密码 | 空 |

root 初始密码留空是为了满足首次启动设置要求。如果路由器可能被可信局域网之外的设备访问，请在首次登录后立即设置密码。

## 构建方法

向 `main` 分支推送提交，或者打开 **Actions → Build Netcore N30 Pro firmware → Run workflow** 手动触发构建。

构建成功后，固件会同时保存为工作流产物并发布到 GitHub Release。刷机时请使用文件名包含 `netcore_n30pro` 的 `sysupgrade.itb` 镜像。

工作流会缓存下载的源码归档、主机构建工具、目标工具链和 ccache 编译结果。首次构建会初始化缓存；后续使用相同 ImmortalWrt 源码版本的构建可以复用缓存。

## 设备支持来源

DTS 和镜像定义基于 [huladabang/openwrt-n30pro](https://github.com/huladabang/openwrt-n30pro)，对应的硬件平台为 MT7981B、512 MB 内存和 128 MB SPI-NAND。

GitHub Actions 使用 [padavanonly/immortalwrt-mt798x-6.6](https://github.com/padavanonly/immortalwrt-mt798x-6.6) 的 `openwrt-24.10-6.6` 分支进行构建。构建过程中还会修正固定源码版本中缺失的 MediaTek HNAT 通知常量。

## 刷机警告

只能通过兼容的 Bootloader 或现有的兼容 OpenWrt 系统刷入 N30 Pro 的 `sysupgrade.itb` 镜像。除非已经单独确认 Bootloader 和内存布局完全匹配，否则不要刷入构建生成的 Bootloader 文件。
