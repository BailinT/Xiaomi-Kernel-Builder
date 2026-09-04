# BailinT 小米 Non-GKI 内核合集构建仓库

本仓库整合两条小米 Non-GKI（老内核）构建线，统一用 GitHub Actions 出包。**所有构建仅适配 MIUI / 澎湃 OS（HyperOS），不构建 AOSP/类原生变体。**

## 支持的设备 / Supported Devices

### 5.4 系列（SM8350 骁龙888 家族）

| 设备代号 / Codename | 设备名称 / Device Name | 构建入口 / Workflow | 状态 |
|---|---|---|---|
| haydn | Redmi K40 Pro / 小米11X Pro / Mi 11i | 5.4 单编 haydn + 5.4 全机型矩阵 | ✅ |
| vili | Redmi K40 Pro+ / 小米11T Pro | 5.4 全机型矩阵 | ✅ |
| venus | 小米11 / Xiaomi 11 | 5.4 全机型矩阵 | ✅ |
| star | 小米11 Ultra / Xiaomi 11 Ultra | 5.4 全机型矩阵 | ✅ |
| odin | 小米MIX 4 / Xiaomi MIX 4 | 5.4 全机型矩阵 | ✅ |
| mars | 小米11 Pro / Xiaomi 11 Pro | 小米11 Pro mars 测试（专用 workflow） | 🧪 测试 |
| cetus | 机型待考证 / TBD | 暂未收录 | ➖ |

> 5.4 出包说明：矩阵与单编走 EndCredits 树（5.4.283，clang r416183b）；mars 测试走 MiYume/Hushangda 树（5.4.302，Neutron clang 18.1.x）。Root 方案统一 ReSukiSU + SuSFS。

### 4.19 系列（SM8250 骁龙865/870 家族）

| 设备代号 / Codename | 设备名称 / Device Name |
|---|---|
| psyche | Xiaomi 12X |
| thyme | Xiaomi 10S |
| umi | Xiaomi 10 |
| cmi | Xiaomi 10 Pro |
| cas | Xiaomi 10 Ultra |
| apollo | Xiaomi 10T / Redmi K30S Ultra |
| munch | Redmi K40S / POCO F4 |
| lmi | Redmi K30 Pro / POCO F2 Pro |
| alioth | Xiaomi 11X / POCO F3 / Redmi K40 |
| elish | Xiaomi Pad 5 Pro |
| enuma | Xiaomi Pad 5 Pro 5G |
| dagu | Xiaomi Pad 5 Pro 12.4 |
| pipa | Xiaomi Pad 6 |

> 4.19 出包说明：「内核构建 - 4.19 单机型」下拉选机型，「内核构建 - 4.19 全机型矩阵」一键全编。KSU 开关可选：ReSukiSU-SuSFS 包带 root，NoKernelSU 包配 Magisk/APatch。源码树 fork 自 AstideLabs（android17-aptusitu 分支，4.19 + 5.15/5.10 backport）。

## 构建入口 / Workflows

| Workflow | 用途 |
|---|---|
| 内核构建 - Redmi K40 Pro haydn (5.4 MIUI) | K40 Pro 单编，最快出包 |
| 内核构建 - 5.4 全机型矩阵 (SM8350 全家族) | 5 台 SM8350 并行 + 可选 Release |
| 内核构建 - 小米11 Pro mars 测试 (5.4 HyperOS) | 小米11 Pro 专用（MiYume 树） |
| 内核构建 - 4.19 单机型 (SM8250 全家族) | 13 款 SM8250 下拉选编 |
| 内核构建 - 4.19 全机型矩阵 (SM8250 全家族) | SM8250 全编 + 可选 Release |

## 刷机注意

- 产物为 AnyKernel3 zip，**只刷 boot 分区**（5.4 系不动 dtbo）；刷前务必备份原厂 boot。
- 5.4 源码树 sublevel 与设备当前固件内核不一定逐位一致，首次刷入后如遇 WiFi/相机/触屏异常，刷回备份 boot 并反馈 issue。
- 5.4 系产物命名：`<代号>-miui-<时间戳>-Anykernel3.zip`（mars 为 `<代号>-hyperos-<时间戳>-Anykernel3.zip`）。

## 加新机型（5.4 系）

检查单：① 源码树 dts overlay 为 `-sm8350-` 命名 ② 树内有同名 `_QGKI.config`（EndCredits 路线）或 `_defconfig`（MiYume 路线）③ 复制 haydn 配方或 mars 配方改 `DEVICE_CODENAME` / `KERNEL_SOURCE` / `KERNEL_BRANCH` / defconfig 相关值。扫描工具参考 `_tools/scan_devices.py` 的方法（dts/vendor/qcom 全量 overlay 列表）。

By 抖音王德发刷机
