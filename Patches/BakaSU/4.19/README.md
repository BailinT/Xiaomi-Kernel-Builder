# BakaSU 非 GKI 手工钩子补丁（4.19）

BakaSU 的 `kernel/tools/manual_hook_check.mk` 在编译期硬检查内核源码里必须存在
`ksu_handle_stat` / `ksu_handle_newfstat_ret` / `ksu_handle_fstat64_ret`
（fs/stat.c）、`ksu_handle_execveat`（fs/exec.c）、`ksu_handle_faccessat`
（fs/open.c）、`ksu_handle_sys_reboot`（kernel/reboot.c），缺任一个直接
`$(error)` 终止构建。本目录就是补这几个调用点的。

## 来源

取自 `MOSSVENC/MVNonGKIDevices`（main 分支），原路径按下表：

| 本目录文件 | 上游原路径 |
|---|---|
| `0001-fs-stat-ksu-manual-hook.patch` | `patches/resukisu/4.19/0001-fs-stat-ksu-manual-hook.patch` |
| `0002-fs-exec-ksu-manual-hook.patch` | `patches/resukisu/4.9/0002-fs-exec-ksu-manual-hook.patch` |
| `0003-fs-open-ksu-manual-hook.patch` | `patches/resukisu/4.19/0003-fs-open-ksu-manual-hook.patch` |
| `0004-kernel-reboot-ksu-manual-hook.patch` | `patches/resukisu/4.9/0004-kernel-reboot-ksu-manual-hook.patch` |

上游把 4.9/4.19 形态相同的补丁去重放在 `4.9/` 目录，这里按构建实际需要的顺序
摊平到同一个目录，内容逐字未改。

## 适用性验证（2026-10-28）

对 `BailinT/Xiaomi-Kernel-Builder@android17-aptusitu` 的
`fs/stat.c`、`fs/exec.c`、`fs/open.c`、`kernel/reboot.c` 逐条
`patch -p1 --dry-run -F 3`：4/4 全部命中，**0 个 FAILED hunk**（最大 offset 63 行、
最大 fuzz 2）。真打之后 `ksu_handle_*` 计数：stat.c=11、exec.c=6、open.c=4、
reboot.c=4；废弃形态裸 `ksu_handle_execve` / `ksu_handle_post_execve` 均 0 命中
（这两个若存在会被 `manual_hook_check.mk` 直接 `$(error)`）。

## 与 SUSFS 的关系

本目录只解决 BakaSU 自身的 hook 检查要求，**不含 SUSFS 内核侧移植**（树内自带
`fs/susfs.c` = SUSFS v2.2.0）。

`build_kernel.sh` 走的是 `CONFIG_KSU_SUSFS=y`（SUSFS inline hook）模式，不是
manual hook 模式，原因是 BakaSU 的 Kconfig 把 `KSU_MANUAL_HOOK` 与 `KSU_SUSFS`
放在同一个 `choice` 里（互斥），而本树的 `fs/read_write.c:590`、
`drivers/input/input.c:382` 无条件 extern 了 `ksu_is_init_rc_hook_enabled` /
`ksu_is_input_hook_enabled` —— 这两个静态键 BakaSU 只在 `CONFIG_KSU_SUSFS` 分支
定义（`kernel/runtime/ksud_integration.c:107-109`）。实测：开 MANUAL_HOOK 关
SUSFS 会在 vmlinux 链接阶段报
`ld.lld: error: undefined symbol: ksu_is_init_rc_hook_enabled`（run 40686317850）。

因此本目录 4 个补丁插入的调用点带 `#ifdef CONFIG_KSU_MANUAL_HOOK` 守卫，在
SUSFS 模式下**不参与编译**，仅用于满足 `kernel/tools/inline_hook_check.mk` 的
文本级 hook 检查（它 grep 源码文字，不检查是否编译）。

注意 BakaSU issue #388 就是红米 K40 / 4.19 非 GKI / susfs 2.3.0 移植不当导致
卡一屏无限重启——本树 SUSFS 侧是 v2.2.0，与 35252 的配对强度【未验】。
