# High-Resolution Scroll Wheel — ZMK 固件

基于 [ZMK](https://zmk.dev/) 的**磁编码器高分辨率（平滑）滚轮**固件。 核心是一颗 **AS5600** 磁旋转传感器（4096 计数/圈）接在 **Super 52840** 上， 配两颗实体按键，实现一个可蓝牙/USB 连接的滚轮控制器。

转动滚轮在「真·高分辨率平滑滚动」之外，还能通过一颗按键循环切换成 **音量 / 左右方向键 / 翻页** 等模式； 另一颗按键管理蓝牙配对。

一个使用 [ams AS5600](https://ams-osram.com/products/sensor-solutions/position-sensors/ams-as5600-position-sensor) 磁性旋转位置传感器的 [ZMK](https://github.com/zmkfirmware/zmk) 配置，用于“高分辨率滚轮”。  
该项目灵感来自 [Engineer Bo](https://www.youtube.com/@engineerbo) 的视频[《无线高分辨率滚动真神奇](https://www.youtube.com/watch?v=FSy9G6bNuKA)》，应作为使用 [ZMK](https://github.com/zmkfirmware/zmk) 键盘固件的开源替代方案。

目前我已经使用as5600模块和super nrf52840用于替代`adolto` XIAO nrf52840，使用nice nano克隆板子也是跑的通的，我已测试

## 开发目的
给Windows、iPadOS、Android tablet 进行漫画翻页，看日漫和webtoon漫画，高分辨率旋钮看条漫非常舒适，普通鼠标采用的是编码器，精度分辨率不够高

## 设备使用
目前已经在在Linux Windows11 iPadOS26 安卓16 确认不使用软件辅助下，旋钮滚动可以实现高分辨率平滑滚动，MacOS最好配合
[BetterMouse – Comprehensive mouse utility for macOS](https://better-mouse.com/)
参考内容
[买了旋钮，找到了macOS的解决办法。:) ：r/ploopy](https://www.reddit.com/r/ploopy/comments/1lv1i6m/got_the_knob_found_a_macos_workaround/)
将速度调到1，加速度调到7：[https://imgur.com/a/bFp5cEc](https://imgur.com/a/bFp5cEc). 我同意界面确实有点......太多了。不过希望这些建议能帮到你！
[BetterMouse – Comprehensive mouse utility for macOS](https://better-mouse.com/)
目前，MacOS 自行进行平滑处理以实现高分辨率滚动。因此，基于 HID 的高分辨率滚动（如 QMK 实现的那种）效果不佳。关于该话题的更多讨论[可见此处](https://github.com/qmk/qmk_firmware/issues/17585)。
## 硬件

- 淘宝 AS5600磁编码器模块  附带磁铁 直径φ4mmX1.9mm 定位孔尺寸的是M4 板子尺寸为23mmX22mm高度不超4mm
- 单独购买直径6mmX2mm，6mmX2.5mm，6mmX3mm  如果使用我提供的外壳最好使用2.5mm高度的
- 802540-850毫安电池
- 淘宝 无名科技Nologo Super 52840用来替代Seeed XIAO nRF52840，主要是这个开发板非常小而且比XIAO便宜,接口一致
- 贴片轻触开关 SMD4脚6X6X4.3 用于切换层 开发板复位  蓝牙设备切换和蓝牙密钥清除
- 飞线 30AWG 完成直径0.6mm
- 轴承NSK 6808 DDU VV 胶封轴承 内径40 外径52 厚度7mm 重31g，也可以选同样规格的氧化锆陶瓷轴承
- M2X4 滚花铜柱
- 扁平头十字螺丝 M2X10头径4
额外一些必须要的东西电烙铁、万用表、热熔枪（热熔胶）用于固定（因为没有额外画一个pcb板）

---

## 功能特性

- **高分辨率平滑滚动**：启用 `CONFIG_ZMK_POINTING_SMOOTH_SCROLLING`，配合 AS5600 的 4096 计数/圈，滚动细腻无颗粒感。
- **四种滚轮模式**（按 D0 键循环切换）：

| 层   | 名称           | 滚轮行为              | 旋钮顺时针    | 旋钮逆时针   |
| --- | ------------ | ----------------- | -------- | ------- |
| 0   | scroll_layer | 鼠标滚轮（高分辨率平滑滚动）    | 鼠标滚轮向下   | 鼠标滚轮向上  |
| 1   | volume_layer | 音量 +/−            | 音量 −     | 音量 +    |
| 2   | arrow_layer  | 键盘方向键 左/右         | 键盘方向键 右  | 键盘方向键 左 |
| 3   | page_layer   | PageUp / PageDown | PageDown | PageUp  |

- **蓝牙 + USB 双输出**，多配对档位（profile）管理。
- **传感器侧降噪**：内置迟滞、慢速滤波、快速滤波阈值配置，降低静止时的抖动。
- **无需 MOSFET 电源门控**：AS5600 常供电，接线极简。
- 通过 **GitHub Actions** 自动云端编译固件，无需本地搭建工具链。

---

## 硬件需求（BOM）

| 部件                | 说明                     |
| ----------------- | ---------------------- |
| Super 52840       | 主控板（`board: xiao_ble`） |
| **AS5600** 磁编码器模块 | I²C 地址 `0x36`          |
| 径向充磁的**径向磁铁**     | 贴于旋钮轴心正对 AS5600 芯片     |
| 外壳                | 安装磁铁、提供手感的滚轮本体         |
| 2 × 轻触按键          | D0（切层）、D1（蓝牙）          |
| 锂电池（可选）           | 无线供电                   |

### 接线
![AS5600.jpg](https://m.comicplan.top/file/1780931518734_AS5600.jpg)
![XIAO_nRF52840_front_pinout.png](https://m.comicplan.top/file/1780931544229_XIAO_nRF52840_front_pinout.png)


**AS5600（I²C，挂在 XIAO 的 `&xiao_i2c` 上）**

| AS5600    | XIAO nRF52840 |      |
| --------- | ------------- | ---- |
| VCC       | 3.3V-OUT      |      |
| GND       | GND           |      |
| SDA       | D4 (P0.04)    | 不要接反 |
| SCL       | D5 (P0.05)    | 不要接反 |
| DIR       | GND           |      |
| OUT / GPO | 不接            |      |
磁铁:**径向充磁**,居中放在芯片正上方约 0.5–3 mm。 自己根据输出日志测试最佳距离
> overlay 中**未设置 `power-gpios`**，传感器保持常供电，因此不需要 MOSFET 电源开关。 若编译报错说 `&xiao_i2c` 未知，把 overlay 里的 `&xiao_i2c` 改成 `&i2c0`。

**按键（直连扫描，内部上拉，按下拉低 = ACTIVE_LOW）**

每颗按键一脚接对应 D 引脚，另一脚接 GND：

|位置|引脚|nRF52840|功能|
|---|---|---|---|
|0|D0|P0.02|循环切换层（滚轮模式）|
|1|D1|P0.03|轻触＝下一蓝牙档位；长按＝清除当前配对|
## 验证顺序(重要)

[](https://github.com/avalon-plan/zmk-config-high-resolution-scroll-wheel#%E9%AA%8C%E8%AF%81%E9%A1%BA%E5%BA%8F%E9%87%8D%E8%A6%81)

1. **先插 USB**:ZMK 插线时优先走 USB HID,先确认转动旋钮页面会滚。USB 通了再测蓝牙。
2. **不动**:在蓝牙列表里找到 "Hi-Res Scroll" 连接。

`build.yaml` 已带 `zmk-usb-logging` 快照——插 USB 用串口工具能看到驱动日志。 如果完全不滚,在 `config/high-resolution-scroll-wheel.conf` 里打开:

```
CONFIG_ZMK_INPUT_AMS_AS5600_LOG_AGC=y
```

重新编译,看串口:

- 有 `AGC: <数值>` → 把磁铁气隙调到让 AGC 落在量程中点(3.3V 下约 0–128)。
- `Magnet not detected` / `AGC overflow(太弱)` / `AGC underflow(太强)` → 调磁铁。
- 注意:开了状态监控时,**磁铁不对就完全不发滚动事件**,这是正常保护。

## 测试
**第一步：修改配置开启日志** 打开您的 `high-resolution-scroll-wheel.conf`，将以下两行改回 `y`：

Properties

```
CONFIG_ZMK_INPUT_AMS_AS5600_LOG_AGC=y
CONFIG_ZMK_USB_LOGGING=y
```

同时在 `build.yaml` 的 `include` 列表中，确保 `snippet: zmk-usb-logging` 加上了（如果没有可以先不加，只改 conf 通常也够了）。重新编译并刷入。

**第二步：连接电脑读取日志**

1. 用 USB 数据线将板子连上电脑。
    
2. 打开一个串口调试助手（如 Arduino IDE 的串口监视器，或者网页版工具如 `https://webserialtool.com`），波特率设置为 `115200`。
    
3. 转动滚轮，观察屏幕输出：
    
    - 如果显示 **`Magnet not detected`**：根本没检测到磁铁（磁铁拿远了或者接线彻底断了）。
        
    - 如果显示 **`AGC overflow`** 或 **`AGC underflow`**：磁铁太近或太远，或者不是径向磁铁。
        
    - 如果显示 **`AGC: [数字]`**（例如 AGC: 64）：说明磁铁完美识别！如果此时依旧不能滚动，说明是 I2C 冲突或其他软件 BUG。

出现其他错误也可以复制给ai去分析
---

## 仓库结构

```
zmk-config-high-resolution-scroll-wheel/
├── .github/workflows/build.yml        # GitHub Actions 云端编译
├── build.yaml                         # 编译矩阵：要构建哪些 board+shield
├── config/
│   ├── west.yml                       # 依赖清单（含自定义 fork）
│   └── high-resolution-scroll-wheel.conf  # Kconfig 编译选项
├── boards/shields/high-resolution-scroll-wheel/
│   ├── high-resolution-scroll-wheel.zmk.yml      # shield 元数据
│   ├── high-resolution-scroll-wheel.overlay      # 硬件/输入处理器定义
│   ├── high-resolution-scroll-wheel.keymap       # 键位与层
│   ├── Kconfig.shield                            # shield 开关
│   └── Kconfig.defconfig                         # 默认值（键盘名等）
├── zephyr/module.yml                  # 把本仓库声明为 Zephyr 模块
└── LICENSE                            # MIT
```

---

## 工作原理

AS5600 驱动输出的是相对滚轮事件 `INPUT_REL_WHEEL`。固件用 ZMK 的**输入处理器（input processor）**链对其进行分层处理（见 `*.overlay` 的 `as5600_listener`）：

- **第 0 层（base）**：保留 `WHEEL` 事件，经 `zip_scroll_scaler` 缩放后作为真正的高分辨率滚动发送给主机。
- **第 1–3 层**：每层用两步处理（参照 zmk#2967 验证过的写法绕过分层覆盖的返回码 bug）：
    1. `zip_wheel_to_y`（code-mapper）把 `WHEEL` 重映射到 `Y` 轴，再交给该层的**量化器**（quantizer）`zip_keybind_*`，把连续转动量化成离散按键；带 `process-next` 让处理链继续。
    2. 同层再用 `zip_xy_scaler 0 1` 把 X/Y 归零，避免任何残余移动泄漏到 HID（也就不会有“幽灵滚动”）。

量化器参数（每层一致）：

- AS5600 = **4096 计数/圈**；`tick = 128` → 约 **每圈 32 档**。
- **`tick` 越小越灵敏**。想更灵敏就调小，想更钝就调大。
- 若某模式方向相反，**对调该量化器 `bindings` 里的 `+Y` / `-Y` 两行**即可。

---

## 编译固件

本仓库使用 ZMK 官方的 _build-user-config_ GitHub Actions 流程，**无需本地环境**。

1. 把本仓库 push 到你自己的 GitHub（或直接 fork）。
2. 进入仓库的 **Actions** 标签页，等待 `Build ZMK firmware` 工作流跑完（也可在 _Actions_ 里手动触发 `workflow_dispatch`）。
3. 编译成功后，在该次运行的 **Artifacts** 区下载 `firmware.zip`，里面是 `.uf2` 文件。
4. 也可以随便修改下readme内容，比如添加一个字，**Actions**会自己跑动，点击**Actions** 标签页-点击修改的README.MD文件名-点击Summary，鼠标拖到最小下面就会出现名为firmware的ZIP压缩里面包括high-resolution-scroll-wheel-xiao_ble-zmk.uf2和settings_reset-xiao_ble-zmk.uf2
5. 如何自己修改我的内容出错请把错误丢给ai，先把要编译的ZMK代码给ai再给编译出错的给ai即可。

`build.yaml` 当前会编译两个目标：

```yaml
include:
  - board: xiao_ble
    shield: high-resolution-scroll-wheel    # 主固件
  - board: xiao_ble
    shield: settings_reset                  # 设置重置固件（用于清空配置）
```

---

## 刷写

1. 用数据线连接 XIAO BLE。
2. **快速双击复位键**进入 UF2 引导模式，电脑上会出现一个 U 盘盘符（`XIAO-BLE` 之类）。
3. 把对应的 `.uf2` 拖进该盘符，板子会自动重启并加载新固件。

如需彻底清空保存的配置/配对，先刷 `settings_reset` 固件，再刷回主固件。

---

## 按键 / 层速查

```
D0（切层）:  层0 滚动 → 层1 音量 → 层2 方向键 → 层3 翻页 → 回到层0
D1（蓝牙）:  轻触     = 切换到下一个蓝牙配对档位 (BT_NXT)
            长按≥500ms = 清除当前档位的配对 (BT_CLR)
```

- 长按阈值由 keymap 中 `bt_ht` 的 `tapping-term-ms = <500>` 控制，太容易误清就调大。
- 想一次清空**所有**档位：把 `bt_clr` 宏里的 `&bt BT_CLR` 改成 `&bt BT_CLR_ALL`。

---

## 配置与调参

主要选项在 `config/high-resolution-scroll-wheel.conf`：

```conf
# 指针 + 高分辨率（平滑）滚动
CONFIG_ZMK_POINTING=y
CONFIG_ZMK_POINTING_SMOOTH_SCROLLING=y

# AS5600 驱动
CONFIG_ZMK_INPUT_AMS_AS5600=y

# 传感器侧降噪（减少静止抖动）
CONFIG_ZMK_INPUT_AMS_AS5600_HYSTERESIS_2LSB=y
CONFIG_ZMK_INPUT_AMS_AS5600_SLOW_FILTER_16X=y
CONFIG_ZMK_INPUT_AMS_AS5600_FAST_FILTER_THRESHOLD_7LSB=y

# 强制启用 BLE/USB 端点（修复 resolution_multipliers 数组越界）
CONFIG_ZMK_BLE=y
CONFIG_ZMK_USB=y
CONFIG_BT=y
CONFIG_USB_DEVICE_STACK=y

# 增大蓝牙发送缓冲，应对高频滚动
CONFIG_BT_L2CAP_TX_BUF_COUNT=10
CONFIG_BT_CONN_TX_MAX=10

# 无数字键设备：关闭蓝牙配对码输入
CONFIG_BT_SMP_ALLOW_UNAUTH_OVERWRITE=y
CONFIG_ZMK_BLE_PASSKEY_ENTRY=n
```

常见调整：

- **滚动灵敏度**：改各 `zip_keybind_*` 的 `tick`（128 默认），以及 base 层 `zip_scroll_scaler` 的倍率。
- **滚动/按键方向**：对调对应量化器 `bindings` 的 `+Y`/`-Y` 行。
- **降噪强弱**：增减 `*_HYSTERESIS_*`、`*_SLOW_FILTER_*`、`*_FAST_FILTER_*` 三项。

---
## 如何调整各层滚动方向

四层的「反向」用两套不同机制——第 1–3 层是按键量化器,第 0 层是滚动缩放器。改动都在 `boards/shields/high-resolution-scroll-wheel/high-resolution-scroll-wheel.overlay`。

### 第 1–3 层（音量 / 方向键 / 翻页）：对调两行

这三层先由 `zip_wheel_to_y` 把滚轮映射成 Y 轴,再交给量化器。量化器 `bindings` 顺序固定为 `<+X -X +Y -Y>`,最后两项 `+Y`/`-Y` 对应转动的两个方向。**对调这两项即可反向**,哪层不对就只改哪层:

```dts
/* 层1 音量 */   bindings = <&none>, <&none>, <&kp C_VOL_UP>, <&kp C_VOL_DN>;  /* ←→ 对调末两项反向 */
/* 层2 方向键 */ bindings = <&none>, <&none>, <&kp RIGHT>,    <&kp LEFT>;     /* ←→ 对调末两项反向 */
/* 层3 翻页 */   bindings = <&none>, <&none>, <&kp PG_UP>,    <&kp PG_DN>;     /* ←→ 对调末两项反向 */
```

### 第 0 层（高分辨率滚动）：加/去掉反向变换器

第 0 层没有按键可对调,走的是 `&zip_scroll_scaler 1 2`。ZMK 的预定义实例 `&zip_scroll_transform` 专门变换滚轮事件,配 `INPUT_TRANSFORM_Y_INVERT` 翻转竖直滚动方向。两步:

1. overlay 顶部 include 区已加上 `#include <dt-bindings/zmk/input_transform.h>`。
2. `as5600_listener` 的基础处理器行(在所有 `xxx_keys` 子节点之前)在「反向」与「原向」之间切换:

```dts
/* 反向（当前）*/ input-processors = <&zip_scroll_transform INPUT_TRANSFORM_Y_INVERT &zip_scroll_scaler 1 2>;
/* 原向         */ input-processors = <&zip_scroll_scaler 1 2>;
```

因为第 1–3 层各有自己的 override、会跳过这条基础处理器,所以该反向**只影响第 0 层**。


> 📌 本仓库附带的 overlay **四层方向均已相对原版反向**,且每处都有注释标明原始写法,想把任意一层改回原向,按上面对应方式还原即可。

---
## 首次上电：磁铁气隙校准

AS5600 对磁铁与芯片之间的**气隙**很敏感。`.conf` 里预留了一行 AGC 日志开关：

```conf
# 取消注释以通过 USB 串口打印 AGC + 状态，用来调整磁铁气隙
# CONFIG_ZMK_INPUT_AMS_AS5600_LOG_AGC=n   ← 调成 =y 开启
```

1. 把该行改为 `CONFIG_ZMK_INPUT_AMS_AS5600_LOG_AGC=y` 并编译刷入。
2. USB 连接，用串口工具查看 AGC 读数。
3. **微调磁铁高度，让 AGC 落在量程中点附近**（既不饱和也不太弱）。
4. 调好后关闭该选项（`=n` 或重新注释），重新编译刷入正式固件。

---
## 电源管理与充电

### 待机与深度睡眠

ZMK 有两级省电状态，靠下面几个选项控制（写在 `config/high-resolution-scroll-wheel.conf`）：

```conf
CONFIG_ZMK_IDLE_TIMEOUT=60000        # 无操作 60s 后进入 idle（轻度待机）
CONFIG_ZMK_SLEEP=y                   # 启用深度睡眠（deep sleep）
CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=900000 # 无操作 900s（15min）后进入深度睡眠
```

含义：

- **`CONFIG_ZMK_IDLE_TIMEOUT`**（整数，毫秒，默认 `30000`）：无操作多久后进入 **idle 状态**。idle 是轻度省电——熄灭屏幕/灯光、降低扫描频率，但**蓝牙连接保持**，动一下滚轮立刻恢复。这里设 `60000` = 60 秒。
- **`CONFIG_ZMK_SLEEP`**（布尔，默认关）：启用 **深度睡眠**。开启后,长时间无操作会进入软件关机级别的低功耗：**断开所有蓝牙连接、关闭外设**，靠按键/转动唤醒(唤醒后需重连)。它依赖硬件的 `HAS_POWEROFF` 支持(XIAO nRF52840 满足)。
- **`CONFIG_ZMK_IDLE_SLEEP_TIMEOUT`**（**整数，毫秒**，默认 `900000` = 15 分钟）：无操作多久后从 idle 进一步进入深度睡眠。**注意它是数值，不是 `=y`**；写成 `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=y` 是错的，会被当未知/无效值。

> 💡 深度睡眠在**检测到 USB 连接时会被自动忽略**——也就是插着线（比如正在充电）时设备不会休眠。这点和下面的大电流充电正好对得上：充电时引脚状态不会因休眠而丢失。

### 大电流充电（200mA）

XIAO nRF52840 / 兼容板（如 super52840）用 **P0.13** 选择电池充电电流：引脚为输入/高电平 = 100mA（小电流，默认），输出**低电平** = 200mA（大电流）。Arduino 例子里那个 `pin 22` 就是这颗 P0.13。

ZMK 没有 Arduino 的 `loop()`，改用 **GPIO hog** 在系统初始化时把 P0.13 固定为输出低电平。在 `boards/shields/high-resolution-scroll-wheel/high-resolution-scroll-wheel.overlay` 末尾加：

```dts
&gpio0 {
    hicharge_hog {
        gpio-hog;
        gpios = <13 GPIO_ACTIVE_HIGH>;
        output-low;        /* 把 P0.13 拉低 → 大电流充电 (200mA) */
    };
};
```

- `GPIO_ACTIVE_HIGH` + `output-low` → P0.13 物理低电平 = 200mA。想回 100mA：把 `output-low` 改成 `output-high`，或删掉整个节点走默认。
- `CONFIG_GPIO_HOGS` 一般在存在 hog 节点时自动启用；若没生效，在 `.conf` 补 `CONFIG_GPIO_HOGS=y`。

> ⚠️ **电池容量要扛得住 200mA。** 对 ≥400mAh 电芯（≤0.5C）很安全；若用 100mAh 这类小电芯，200mA ≈ 2C 偏高，伤寿命甚至有风险。开大电流前先确认电池规格。

---

## 依赖（`config/west.yml`）

本固件依赖几个**自定义 fork**，并非全部来自 ZMK 官方：

|项目|来源|说明|
|---|---|---|
|`zmk`|`zettaface/zmk`（`main`）|修复了 input-processor 分层覆盖返回码 bug（zmk#2967）。想回官方版：把 remote 改回 `zmkfirmware`、revision 改回 `v0.3.0`。|
|`zmk-driver-ams-as5600`|`avalon-plan`（`main`）|AS5600 磁编码器驱动。|
|`zmk-input-processor-keybind`|`zettaface`（`main`）|把转动量化为按键的输入处理器模块。|

> ⚠️ 若改用官方 `v0.3.0`，分层覆盖的 bug 可能导致跨层误触发（“幽灵滚动”）。 另外 `west.yml` 中标注 `← 改成你的` 的 `url-base`，请按你实际托管 fork 的账号修改。

---

## 故障排查

|现象|可能原因 / 处理|
|---|---|
|编译报 `&xiao_i2c` 未知|把 overlay 里的 `&xiao_i2c` 改为 `&i2c0`。|
|`resolution_multipliers` 数组越界编译错误|确认 `.conf` 中 BLE/USB 端点全部强制启用（已默认开启）。|
|切到非滚动层后仍有滚动残余|确认使用的是 `zettaface/zmk` fork；官方版有分层覆盖 bug。|
|滚动方向反了|对调对应量化器 `bindings` 的 `+Y` / `-Y` 行。|
|静止时滚轮乱跳|检查磁铁气隙（AGC 校准）；增强 `.conf` 的滤波/迟滞选项。|
|高速滚动丢事件|已通过增大 `BT_L2CAP_TX_BUF_COUNT` / `BT_CONN_TX_MAX` 缓解，可继续上调。|
|蓝牙连不上 / 配对乱|刷 `settings_reset` 固件清空，再刷主固件重新配对。|

---
## 已上传编译好的固件
settings_reset-xiao_ble-zmk.uf2
high-resolution-scroll-wheel-xiao_ble-zmk.uf2



## 外壳
- 现成3D模型
上传的是配合和打印版本，装配体可以自己选择修改，关于磁铁间隙距离，我目前使用下来是没有问题的，如果觉得不够完美那么请自己修改。
AS5600模块的定位板的M4孔感觉比模型画的大，所以可以使用小刀轻微扩孔，
底座可以自己修改，上盖我参考的是[Engineer Bo](https://www.youtube.com/@engineerbo) 上盖尺寸，不过由于我之前已经制作过直径为70直径的旋钮，并且NSK也是6808，轴承是现成的，因为我之前用的是EC11编码器和TTC5mm编码器，外壳参考的是geekhack上的NEO KNOB KN01

](https://geekhack.org/index.php?topic=116864.0)
3D打印材质为嘉立创的3301PA   白色
如果你需要CNC上盖一定要注意上盖内径52一定要最好配合参考的视频博主，也就是说在轴承的外径在套一个打印的TPU圈（柔软比较好过盈配合），然后在套入nsk轴承，不然CNC出来铝合金未必能直接塞进外径52的轴承，CNC铝合金和NSK轴承可不好塞啊，一定要注意这个特别重要
参考图
![Screenshot_2026-05-31-21-56-30-32_64ef5fc2000c1caa954c114bb372e1d5.jpg](https://m.comicplan.top/file/1780980535227_Screenshot_2026-05-31-21-56-30-32_64ef5fc2000c1caa954c114bb372e1d5.jpg)

## 成品展示
最终成品是直径70mm 全高是28.5mm
![1780980689383.jpg](https://m.comicplan.top/file/1780980776791_1780980689383.jpg)
![1780980689429.jpg](https://m.comicplan.top/file/1780980768203_1780980689429.jpg)
![1780980689452.jpg](https://m.comicplan.top/file/1780980769860_1780980689452.jpg)

## 高分辨旋钮测试环节
网页测试 
[So You Think You Can Scroll](https://engineerbo.github.io/so-you-think-you-can-scroll/)


## 资料说明
- 无名科技Nologo Super 52840    [Super nRF52840 介绍](https://www.nologo.tech/product/keyboard/keyboard_controll/super52840/super528401.html) Super52840是Xiao nRF52840的替代产品，Super52840 使用的是 Xiao nRF52840 的固件，也就是说你把Super52840当成Xiao nRF52840就行了，编译ZMK固件的时候使用xiao_ble作为开发板名称
- Xiao nRF52840 [Seeed Studio XIAO nRF52840 系列入门指南 | Seeed Studio Wiki](https://wiki.seeedstudio.com/cn/XIAO_BLE/)
## 未来
- 目前的初版外壳设计一般，未来继续修改，并且没有是一瓶CNC的铝合金上盖，且对
- 使用WS2812B 灯珠  雾状款 表面乳白色胶体，光不会那么刺眼，颜色好看点用于切换层显示  切换哪个层闪烁一次颜色以及电池电量
- 加个电源开关不用时关闭，更好的省电
- 加一个MOSFET和100k电阻节省电量 ，这玩意没PCB太难焊接了，小的一批
- 因为对代码不熟悉，所有都是丢给claude 4.8读取写，所以没有搞明白怎么使用ZMK studio 的在线改键，看了AI回复说是无法修改

## 致谢

- [ZMK Firmware](https://zmk.dev/) 及社区
- `zettaface` 提供的 zmk fork 与 keybind 输入处理器
- `adolto` 提供的 AS5600 驱动和ZMK固件
- `joelTXW` 提供的ZMK固件
- ZMK#2967 中验证分层覆盖写法的相关贡献者
