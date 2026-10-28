# HWID 白名单校验（第四链 / 小米 Non-GKI）

刷了本目录注入出来的内核的机器，开机时若 `androidboot.*` 里的设备 ID 不在
`allowlist.txt` 里，内核直接 `panic()` —— 表现为**卡 logo 反复重启**，不是"功能受限"。
所以加错条目、抄错 ID 的后果就是砖到刷回原厂 boot 为止。

## 文件

| 文件 | 作用 |
|---|---|
| `hwid_lock.c` | 内核侧校验代码，被注入到 `<树>/init/hwid_lock.c` 并挂进 `init/Makefile` 的 `obj-y` |
| `inject_hwid_lock.sh` | 注入器：把 allowlist 生成 `init/hwid_allowlist.h`、拷 .c、改 Makefile |
| `allowlist.txt` | 白名单，一行一个 ID |
| `probe_hwid.sh` | 设备端探针，跑在**能开机的**内核上，把五个 key 全打一遍 |

来源：`BailinT/GKI_BakaSU_SUSFS-hwid` 的 `hwid/` 四件套，逐字搬过来，
**只改了一处**——`hwid_lock.c` 的 `#include <linux/bootconfig.h>` 加了
`#if IS_ENABLED(CONFIG_BOOT_CONFIG)` 守卫。原因：bootconfig 是 Linux 5.0 才有的，
本链是 4.19 非 GKI 树，`include/linux/bootconfig.h` 不存在（实测 404），
不加守卫直接编译失败。`hwid_read_key()` 里用 `xbc_find_value()` 的那段本来就包在
`#ifdef CONFIG_BOOT_CONFIG` 里，4.19 上走 `hwid_from_cmdline()` 解析 `boot_command_line`。

## 内核读哪几个 key

按顺序探测，**任一命中即放行**（`hwid_keys[]`）：

1. `androidboot.chipid` — 欧加系
2. `androidboot.cpuid` — **小米/红米系，K40 就是这个**
3. `androidboot.emmcid` — 努比亚/红魔
4. `androidboot.serialno` — vivo/iQOO/联想/摩托/默认
5. `oplusboot.serialno` — 欧加系

比对规则（`hwid_match()`）：先 `strcasecmp` 精确比，两边都能按 16 位无符号解析时再按
数值比。所以 `0x0000044864acd92f` / `44864acd92f` 算同一台；但**超长 ID（如小米
256bit/64 hex）`kstrtoull` 会溢出**，那种只能靠逐字符精确匹配，allowlist 必须写
cmdline 原值。

## 加一台新设备的流程

1. 在那台机器（跑原厂或任何能开的内核）上：
   `adb push hwid/probe_hwid.sh /sdcard/ && adb shell su -c "sh /sdcard/probe_hwid.sh"`
   读 `/sdcard/hwid_probe.log`，找 `androidboot.` 那几行的**原始值**。
2. 把原值加进 `allowlist.txt`（小写、一行一个、带上机型和日期注释）。
   条目格式限制：`^[0-9a-z._:-]{4,128}$`，不合规注入器直接报错退出。
3. 走「内核构建 - 4.19 单机型（校验版）」那条 workflow 出包。

## 先跑 dry run 再出货（强烈建议）

`inject_hwid_lock.sh` 认两种开关：环境变量 `HWID_LOCK_DRY_RUN=1`，或本目录下存在
`DRYRUN` 文件。dry run 下内核**不 panic**，只往 dmesg 打：

```
HWID lock: found androidboot.cpuid=0xf3790323
HWID lock: device authorized via androidboot.cpuid
```

新机型第一次上，先出一次 dry run 包刷进去，`dmesg | grep "HWID lock"` 确认内核
**实际看到的 key 和值**与 allowlist 写的一致，再关掉 dry run 出正式包。
**正式出货的包绝不能带 dry run**（注入器在 dry run 时会打 `::warning::`，
Actions 页面会黄条提示，看到就别发）。
