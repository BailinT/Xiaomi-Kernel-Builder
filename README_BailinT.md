# BailinT 小米 Non-GKI 内核合集构建仓库

本仓库整合两条小米 Non-GKI（老内核）构建线，统一用 GitHub Actions 出包：

## 构建线

| 系列 | 内核版本 | 设备 | workflow | 源码 |
|---|---|---|---|---|
| 4.19（SM8250 家族） | 4.19 | alioth(K40)/apollo/cas/cmi/dagu/elish/enuma/lmi(K30 Pro)/munch(K40S)/pipa/psyche/thyme/umi 共 13 款 | 「Build Kernel」「Build All Devices Kernel」（AstideLabs 原版） | 仓库内（fork 自 AstideLabs/android_kernel_xiaomi_sm8250） |
| 5.4（SM8350 家族） | 5.4.283 | haydn（Redmi K40 Pro / K40 Pro+ / Mi 11X Pro / Mi 11i） | 「内核构建 - Redmi K40 Pro haydn (5.4 MIUI)」 | [BailinT/android_kernel_xiaomi_sm8350-miui](https://github.com/BailinT/android_kernel_xiaomi_sm8350-miui)（fork 自 EndCredits，ASB-2024-10-05） |

## 5.4 线构建配方

- 构建引擎：JackA1ltman/NonGKI_Kernel_Build_2nd 的复合 action（build-env/build-ready/build-process/pack-process 等，已随仓库拷入）+ Bin 工具 + Patches 补丁（含 susfs_patch_to_5.4.patch）
- Root：ReSukiSU（main 分支，shell 方式集成，自动 hook）
- 隐藏：SuSFS v2.2.0（susfs inline hook）
- 工具链：LineageOS clang r416183b + AOSP GCC 4.9（与小米11 venus 同平台 Stable 配方一致）
- defconfig：`vendor/lahaina-qgki_defconfig` + 合并 `vendor/haydn_QGKI.config`、`vendor/xiaomi_QGKI.config`
- 打包：AnyKernel3（[BailinT/AnyKernel3](https://github.com/BailinT/AnyKernel3)），仅刷 boot 分区 Image（A/B 设备，不动 dtbo）
- 产物命名：`haydn-miui-<时间戳>-Anykernel3.zip`

## 首次构建注意

- `BUILD_DEBUGGER=true` + `SKIP_PATCH=true` 已默认开启：SuSFS 补丁如有 .rej 会打包上传（patch_rejected_files artifact），先看完整构建结果再决定是否给 haydn 做专属修复补丁（对照 NonGKI_Kernel_Patches 的 mi11_evox_a16/susfs_fixed.patch 模式，放进 `Patches/` 或远端补丁仓后设 `SUSFS_PATCH_FIXED`）。
- 源码树（5.4.283 / ASB-2024-10）与设备当前 MIUI 固件内核 sublevel 不一定逐位一致，首次刷入前先备份原厂 boot。

## 加新机型（5.4 系）

复制 `build-haydn-miui-5.4.yml` 改 4 个值即可：`DEVICE_CODENAME`、`KERNEL_SOURCE/KERNEL_BRANCH`、`MERGE_CONFIG_FILES`（对应机型 `_QGKI.config`）、workflow 名。设备 dts/defconfig 需已在源码树中（EndCredits 树另含 star/venus/vili/renoir/taoyao/zijin 等 MIUI 机型配置）。

By 抖音王德发刷机
