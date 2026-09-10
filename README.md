# Mouse Trail / 鼠标拖尾

**EN:** A highly customizable mouse cursor trail mod for [Windhawk](https://windhawk.net/) with particle effects, 12 color modes, custom function trails, cursor color extraction, and click effects. Direct2D hardware accelerated, runs as a dedicated process with zero CPU usage when idle.

**中文：** 高度可定制的 [Windhawk](https://windhawk.net/) 鼠标拖尾特效 mod，支持粒子系统、12种颜色模式、自定义函数轨迹、光标取色和点击特效。Direct2D 硬件加速，独立进程运行，静止时零 CPU 占用。

![Trail Effect](https://raw.githubusercontent.com/MCheng404/cursor-motion-blur-enhanced/main/assets/demo_trail.gif)

### More Demos / 更多展示

![Demo 2](https://raw.githubusercontent.com/MCheng404/cursor-motion-blur-enhanced/main/assets/demo_trail_2.gif)

![Demo 3](https://raw.githubusercontent.com/MCheng404/cursor-motion-blur-enhanced/main/assets/demo_trail_3.gif)

![Demo 4](https://raw.githubusercontent.com/MCheng404/cursor-motion-blur-enhanced/main/assets/demo_trail_4.gif)

![Demo 5](https://raw.githubusercontent.com/MCheng404/cursor-motion-blur-enhanced/main/assets/demo_trail_5.gif)

---

## Features / 功能特性

* **4 Trail Shapes / 4 种拖尾形状：** Tapered ribbon / Tapered dot chain / Function curve / Sine wave. / 锥形带 / 类锥形圆链 / 函数曲线 / 正弦波浪。
* **12 Color Modes / 12 种颜色模式：** Single / Gradient (3-color) / Rainbow / Warm / Cool / Neon / Velocity / Stripes / Fire / Aurora / Cursor Extract / Cursor Mix.
* **Particle System / 粒子系统：** Mini particles released from the trail, attracted back to cursor with configurable origin (head/middle/tail/custom), attraction strength, and cursor repulsion force. Shapes: circle / star / hexagram / random mix. / 拖尾释放迷你粒子，全程吸附回光标。释放位置、吸附强度、光标排斥力均可调。支持圆形/五角星/六芒星/随机混合形状。
* **Click Effects / 点击特效：** Starburst particle burst + expanding ripple on left/right click (both toggleable). / 点击时迸发星爆粒子 + 扩散波纹（均可开关）。
* **Cursor Color Extraction / 光标取色：** Real-time pixel color sampling under the cursor (2 modes), with auto complementary-color shift for visibility. / 实时提取光标下方像素颜色（2种模式），支持自动互补色偏移确保醒目。
* **Function Trails / 函数轨迹：** Custom math expressions generate trail curves, 4 built-in presets. / 自定义数学公式生成轨迹曲线，内置4组预设。
* **Delay Rendering / 延迟渲染：** Trail head eases toward the cursor (0-10 adjustable). / 拖尾头部缓动跟随光标（0-10可调）。
* **Fadeout Modes / 淡出模式：** Hard cut / Accelerated shrink / Soft fade. / 硬截断 / 加速收缩 / 软截断。
* **Dynamic Width / 动态宽度：** Trail widens with speed and acceleration. / 移动越快、急转时拖尾越宽。
* **Enhanced Glow / 增强发光：** Dual-layer halo (outer glow + inner bloom), toggleable. / 双层光晕（外晕+内辉），可开关。
* **Game Detection / 游戏检测：** Auto-disable in fullscreen DirectX games. / 全屏 DirectX 游戏时自动禁用。
* **Idle at 0% CPU / 零 CPU 待机：** Window hidden when cursor is stationary and no effects active. / 鼠标静止且无特效时窗口隐藏，CPU 占用为 0%。

## Installation / 安装

1. Install [Windhawk](https://windhawk.net/)
2. Download `mouse-trail.wh.cpp`
3. In Windhawk, click "Create mod" → paste the code → Save

**中文：**
1. 安装 [Windhawk](https://windhawk.net/)
2. 下载 `mouse-trail.wh.cpp`
3. 在 Windhawk 中点击"创建 mod"→ 粘贴代码 → 保存

## Function Trail Variables / 函数轨迹变量

**EN:** Available variables: `t` (normalized 0=head 1=tail), `d` (distance from head in px), `time` (seconds). Functions: sin cos tan exp sqrt abs log. Operators: + - * / ^. Constants: pi e.

**中文：** 自定义公式中可使用：`t`（归一化位置 0=头 1=尾）、`d`（距头部像素距离）、`time`（秒）。支持函数：sin cos tan exp sqrt abs log，运算符：+ - * / ^，常量：pi e。

**Examples / 示例：** `sin(d * 0.15) * 8`, `sin(d * 0.25) * exp(0 - t * 2.5) * 10`

## Color Format / 颜色格式

**EN:** Hex RGB, e.g. `FF0000`=red, `00FF00`=green, `0000FF`=blue, `FFD700`=gold.

**中文：** 自定义颜色使用十六进制 RGB，例如：`FF0000`=红，`00FF00`=绿，`0000FF`=蓝，`FFD700`=金。

## Architecture / 架构

- **Mod type:** Windhawk Tool Mod (`@include windhawk.exe`), runs in a dedicated process
- **Rendering:** Direct2D hardware accelerated layered window (WS_EX_LAYERED)
- **Compiler options:** `-ld2d1 -lole32 -lgdi32 -lshell32`

## Author / 作者

Developed by [MCheng404](https://github.com/MCheng404).

## License / 许可证

MIT License.
