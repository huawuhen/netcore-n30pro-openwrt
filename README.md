# Netcore N30 Pro OpenWrt firmware builder

This repository builds a Netcore N30 Pro firmware with GitHub Actions.

## Included

- LuCI with Chinese language support, exposed over **HTTP only**
- PassWall2 and OpenClash
- sing-box, Xray-core and v2ray geodata
- DDNS, UPnP, Samba4, ttyd, Wake-on-LAN, traffic statistics and AdGuard Home
- USB modem and common USB-network drivers

Initial device settings:

| Item | Value |
| --- | --- |
| LAN address | `192.168.6.1` |
| LuCI | `http://192.168.6.1/` |
| LuCI HTTPS | disabled |
| User | `root` |
| Password | blank |

The blank root password is intentional for the requested first-boot setup. Set
a password immediately after logging in if the router is reachable by anyone
outside your trusted LAN.

## Build

Push to `main`, or open **Actions → Build Netcore N30 Pro firmware → Run
workflow**.  On success, the firmware is attached both to the workflow
artifact and to a GitHub Release. Use the `*netcore_n30pro*sysupgrade.itb`
image.

The workflow caches the downloaded source archives, host build tools, target
toolchain and ccache objects. The first build initializes the cache; later
builds with the same ImmortalWrt source revision reuse it automatically.

## Device support source

The DTS and image definition are derived from
[huladabang/openwrt-n30pro](https://github.com/huladabang/openwrt-n30pro),
which identifies the platform as MT7981B / 512 MB RAM / 128 MB SPI-NAND. The
Action builds the `openwrt-24.10-6.6` branch of
[padavanonly/immortalwrt-mt798x-6.6](https://github.com/padavanonly/immortalwrt-mt798x-6.6).
It also applies a small compatibility patch for a missing set of MediaTek HNAT
notification constants in that pinned source revision.

Only flash the N30 Pro `sysupgrade.itb` image through a compatible bootloader
or an existing compatible OpenWrt installation. Do not flash the generated
bootloader artifacts unless you have separately verified your bootloader and
memory layout.
