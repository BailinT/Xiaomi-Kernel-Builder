# ApartTUSITU's Xiaomi SM8250 Kernel  

## Warning
**中文:**  
内核源码仍在开发中，可能会导致一些不可预料的问题，请谨慎使用。  
**English:**  
The kernel source code is still under development and may cause some unpredictable problems. Please use it with caution.  

---

## 支持的设备（4.19 / SM8250）/ Supported Devices (4.19)
| 设备代号 / Codename  | 设备名称 / Device Name            |
|---------------------|-----------------------------------|
| psyche              | Xiaomi 12X                        |
| thyme               | Xiaomi 10S                        |
| umi                 | Xiaomi 10                         |
| munch               | Redmi K40S / POCO F4              |
| lmi                 | Redmi K30 Pro / POCO F2 Pro       |
| cmi                 | Xiaomi 10 Pro                     |
| cas                 | Xiaomi 10 Ultra                   |
| apollo              | Xiaomi 10T / Redmi K30S Ultra     |
| alioth              | Xiaomi 11X / POCO F3 / Redmi K40  |
| elish               | Xiaomi Pad 5 Pro                  |
| enuma               | Xiaomi Pad 5 Pro 5G               |
| dagu                | Xiaomi Pad 5 Pro 12.4             |
| pipa                | Xiaomi Pad 6                      |

---

## 支持的设备（5.4 / SM8350 骁龙888）/ Supported Devices (5.4)

| 设备代号 / Codename | 设备名称 / Device Name | 构建入口 / Workflow | 状态 |
|---------------------|------------------------|---------------------|------|
| haydn               | Redmi K40 Pro / 小米11X Pro / Mi 11i | 5.4 单编 haydn + 5.4 全机型矩阵 | ✅ |
| vili                | Redmi K40 Pro+ / 小米11T Pro | 5.4 全机型矩阵 | ✅ |
| venus               | 小米11 / Xiaomi 11 | 5.4 全机型矩阵 | ✅ |
| star                | 小米11 Ultra / Xiaomi 11 Ultra | 5.4 全机型矩阵 | ✅ |
| odin                | 小米MIX 4 / Xiaomi MIX 4 | 5.4 全机型矩阵 | ✅ |
| mars                | 小米11 Pro / Xiaomi 11 Pro | 小米11 Pro mars 测试（专用） | 🧪 |
| cetus               | 机型待考证 / TBD | 暂未收录 | ➖ |

**中文:**  
5.4 系列走仓库内 Non-GKI 构建引擎（源码外挂）：矩阵/单编基于 EndCredits 树（5.4.283），小米11 Pro 基于 MiYume/Hushangda 树（5.4.302）。Root 统一 ReSukiSU + SuSFS，**仅适配 MIUI / 澎湃 OS**。产物 `<代号>-miui-<时间戳>-Anykernel3.zip`，只刷 boot 分区，刷前备份原厂 boot。  
**English:**  
The 5.4 series uses the in-repo Non-GKI build engine (external kernel sources): matrix/single builds are based on the EndCredits tree (5.4.283), Xiaomi 11 Pro on the MiYume/Hushangda tree (5.4.302). Root: ReSukiSU + SuSFS. **MIUI / HyperOS only.** Output: `<codename>-miui-<timestamp>-Anykernel3.zip`, boot partition only — back up your stock boot first.

---


## 目录 / Table of Contents
- [支持的设备（4.19 / SM8250）](#支持的设备419--sm8250--supported-devices-419)  
- [支持的设备（5.4 / SM8350）](#支持的设备54--sm8350-骁龙888-supported-devices-54)  
- [简介 / Introduction](#简介--introduction)  
- [特性 / Features](#特性--features)  
- [社区 / Community](#社区--community)  
- [支持的设备 / Supported Devices](#支持的设备--supported-devices)   
- [构建方法 / Build Instructions](#构建方法--build-instructions)  
  - [快速构建 / Quick Build](#快速构建--quick-build)  
  - [手动构建 / Manual Build](#手动构建--manual-build)  

---

## 简介 / Introduction
**中文:**  
该 repo 基于 [LineageOS/android_kernel_qcom_sm8250](https://github.com/LineageOS/android_kernel_qcom_sm8250)，结合了 [Strawing 的仓库](https://github.com/liyafe1997/kernel_xiaomi_sm8250_mod) 和 [UtsavBalar1231 的仓库](https://github.com/UtsavBalar1231/kernel_xiaomi_sm8250)，以及原本的 [LineageOS/android_kernel_xiaomi_sm8250](https://github.com/LineageOS/android_kernel_xiaomi_sm8250)。一些提交同样来源于开源社区中的其他开发者。

**English:**  
This repository is based on [LineageOS/android_kernel_qcom_sm8250](https://github.com/LineageOS/android_kernel_qcom_sm8250), incorporating [Strawing's repository](https://github.com/liyafe1997/kernel_xiaomi_sm8250_mod) and [UtsavBalar1231's repository](https://github.com/UtsavBalar1231/kernel_xiaomi_sm8250), as well as the original [LineageOS/android_kernel_xiaomi_sm8250](https://github.com/LineageOS/android_kernel_xiaomi_sm8250). Some commits also originate from other developers in the open-source community.  

---

## 特性 / Features
**中文:**  
本内核支持 [ReSukiSU](https://github.com/ReSukiSU/ReSukiSU) & [SuSFS](https://gitlab.com/simonpunk/susfs4ksu)。请自行安装 ReSukiSU 的管理器。NoKernelSU 版本支持应用 Magisk 和 APatch(及他们的分支)。  

**Release** 里的编译好的内核成品由 `android17-aptusitu` 分支编译，应当能在原版 MIUI/HyperOS 和第三方的基于 AOSP 的各种 Android11-17 的 ROM 上使用。欢迎大家尝试并反馈(提 Issue 或 Pull Requests)! 酷友们到 [这个帖子](https://www.coolapk.com/feed/69700122) 讨论或反馈，也可以加入 QQ 群，或者给我私信反馈!  

以下是一些具体的功能:  
1. F2FS 开启了 realtime discard 以更好地 TRIM 闪存  
2. 支持 EROFS  
3. zRAM 支持 LZO、LZ4、LZ4HC、ZSTD 等压缩算法，开启了 ZRAM_WRITEBACK，升级了 LZ4 和 ZSTD  
4. 向后移植 5.15 BPF 和 clone3(支持安卓 16/17)  
5. 引入 [LE9EC](https://github.com/hakavlad/le9-patch) 以优化内存  
6. 向后移植 5.10 的 Binder，MIUI 构建引入来自 xaga 的 millet，AOSP 构建引入 [Re:Kernel](https://github.com/Sakion-Team/Re-Kernel)  
7. 修复[电量卡在 1% 的问题](https://github.com/liyafe1997/Xiaomi-fix-battery-one-percent)，并且支持解容  
8. 集成 [BBG(Baseband-guard)](https://github.com/vc-teahouse/Baseband-guard)    

**English:**  
This kernel supports [ReSukiSU](https://github.com/ReSukiSU/ReSukiSU) & [SuSFS](https://gitlab.com/simonpunk/susfs4ksu).  
Please install the ReSukiSU Manager by yourself.  
The NoKernelSU version supports Magisk and APatch (and their forks).  

The prebuilt kernel in the **Release** section is compiled from the `android17-aptusitu` branch, and should work on stock MIUI/HyperOS as well as third-party AOSP-based ROMs for Android 11–17.  
Feedback is welcome (via Issues or Pull Requests)! Coolapk users can join the discussion in [this post](https://www.coolapk.com/feed/69700122), you can also join my QQ group, or send me private feedback.  

Below are some of the key features:  
1. F2FS with realtime discard enabled for improved flash TRIM behavior  
2. Support for EROFS  
3. zRAM with support for multiple compression algorithms, including LZO, LZ4, LZ4HC, and ZSTD, enabled ZRAM_WRITEBACK, upgraded LZ4 and ZSTD  
4. Backported BPF from Linux 5.15 and clone3 (Android 16/17 compatible)  
5. Introduced [LE9EC](https://github.com/hakavlad/le9-patch) to optimize memory  
6. Backported Binder from 5.10; MIUI builds incorporate millet from xaga, while AOSP builds incorporate [Re:Kernel](https://github.com/Sakion-Team/Re-Kernel)  
7. Fixes [the issue where the battery percentage gets stuck at 1%](https://github.com/liyafe1997/Xiaomi-fix-battery-one-percent), and supports recognizing higher-capacity replacement batteries  
8. Integrate [BBG(Baseband-guard)](https://github.com/vc-teahouse/Baseband-guard)  

---

## 社区 / Community
**中文:**  
欢迎加入 ApartTUSITU's QQ 交流群: [700675046](https://qm.qq.com/q/Md7nXA3Toa)。  

**English:**  
Join ApartTUSITU's QQ Group: [700675046](https://qm.qq.com/q/Md7nXA3Toa)。  

---

## 构建方法 / Build Instructions

### 快速构建 / Quick Build
**中文:**  
1. fork 本仓库(别忘了点个 Star~)  
2. 进入 **Actions**  
3. 如果你要为所有支持的设备编译内核，找到 `内核构建 - 4.19 全机型矩阵 (SM8250 全家族)`，点击 `Run workflow`  
4. 如果你要为单个设备编译内核，找到 `内核构建 - 4.19 单机型 (SM8250 全家族)`， 点击 `Run workflow` 并选择必要内容  

**English:**  
1. Fork this repo (don’t forget to leave a Star~)  
2. Go to **Actions**  
3. If you want to compile the kernel for all supported devices, find `Build All Devices Kernel (Matrix Parallel + Release)` and click `Run workflow`  
4. If you want to compile the kernel for a single device, find `Build Kernel`, click `Run workflow`, and select the necessary options  

---

### 手动构建 / Manual Build
**中文:**  
1. 准备基本构建环境。  
   需要常用工具链 `git`、`make`、`curl`、`bison`、`flex`、`zip` 等，以及一些软件包。  
   - 在 Debian/Ubuntu:  
   ```
   sudo apt install build-essential git curl wget bison flex zip bc cpio libssl-dev ccache tar
   ```
   还需要 `python` (仅有 `python3` 不够)，可安装:  
   ```
   sudo apt install python-is-python3
   ```

   - 在 RHEL/RPM 系统:  
   ```
   sudo yum groupinstall 'Development Tools'
   sudo yum install wget bc openssl-devel ccache tar
   ```

   注意：`build.sh` 中启用了 `ccache`，路径是 `$HOME/.cache/ccache_mikernel`。可修改或删除。  

2. 下载 [ZyC-Clang v16](https://github.com/ZyCromerZ/Clang/releases/tag/16.0.6-20260807-release) 工具链:  
   ```
   mkdir zyc-clang
   cd zyc-clang
   wget https://github.com/ZyCromerZ/Clang/releases/download/16.0.6-20260807-release/Clang-16.0.6-20260807.tar.gz
   tar -zxvf Clang-16.0.6-20260807.tar.gz
   cd ..
   ```

3. 构建:  
   - 不使用 KernelSU:  
     ```
     bash build_kernel.sh TARGET_DEVICE TARGET_OS(省略为全部编译)
     ```
   - 使用 KernelSU:  
     ```
     bash build_kernel.sh TARGET_DEVICE ksu TARGET_OS(省略为全部编译)
     ```

   示例:  
   - lmi (Redmi K30 Pro/POCO F2 Pro) 不使用 KernelSU，为 MIUI 编译:  
     ```
     bash build_kernel.sh lmi miui
     ```
   - cmi (Xiaomi 10 Pro) 使用 KernelSU，为 MIUI 编译:  
     ```
     bash build_kernel.sh cmi ksu miui
     ```
   - umi (Xiaomi 10) 使用 KernelSU，为 AOSP 和 MIUI 编译:  
     ```
     bash build_kernel.sh umi ksu
     ```

   另外，`buildall.sh` 可一次性为所有设备构建。  

**English:**  
1. Prepare the build environment.  
   You need `git`, `make`, `curl`, `bison`, `flex`, `zip`, etc.  
   - On Debian/Ubuntu:  
   ```
   sudo apt install build-essential git curl wget bison flex zip bc cpio libssl-dev ccache tar
   ```
   You also need `python` (not just `python3`):  
   ```
   sudo apt install python-is-python3
   ```

   - On RHEL/RPM-based OS:  
   ```
   sudo yum groupinstall 'Development Tools'
   sudo yum install wget bc openssl-devel ccache tar
   ```

   Note: `ccache` is enabled in `build.sh` (`$HOME/.cache/ccache_mikernel`). You may remove/modify it.  

2. Download [ZyC-Clang v16](https://github.com/ZyCromerZ/Clang/releases/tag/16.0.6-20260807-release) toolchain:  
   ```
   mkdir zyc-clang
   cd zyc-clang
   wget https://github.com/ZyCromerZ/Clang/releases/download/16.0.6-20260807-release/Clang-16.0.6-20260807.tar.gz
   tar -zxvf Clang-16.0.6-20260807.tar.gz
   cd ..
   ```

3. Build:  
   - Without KernelSU:  
     ```
     bash build_kernel.sh TARGET_DEVICE TARGET_OS(omit to build for all)
     ```
   - With KernelSU:  
     ```
     bash build.sh TARGET_DEVICE ksu TARGET_OS(omit to build for all)
     ```

   Example:  
    - lmi (Redmi K30 Pro/POCO F2 Pro) without KernelSU, compile for MIUI:  
      ```
      bash build_kernel.sh lmi miui
      ```
    - cmi (Xiaomi 10 Pro) with KernelSU, compile for MIUI:  
      ```
      bash build_kernel.sh cmi ksu miui
      ```
    - umi (Xiaomi 10) with KernelSU, compile for AOSP and MIUI:  
      ```
      bash build_kernel.sh umi ksu
      ```

   Additionally, `buildall.sh` can build for all supported devices at once.  
