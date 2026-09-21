# Mouse Trail

A highly customizable mouse cursor trail mod for [Windhawk](https://windhawk.net/). Built on native D3D11 + DirectComposition hardware acceleration, featuring 23 color modes, 10 trail render styles, 9 particle shapes, a full Newtonian particle physics system (mass, gravity, collisions, electromagnetic forces, turbulence, fluid coupling), centripetal vortex orbital capture, music-reactive audio physics, 2.5D depth effects, click effects, and text/emoji particles. Runs as an isolated Tool Mod process — zero CPU when idle, full hardware acceleration when active.

**[中文文档](https://github.com/MCheng404/mouse-trail#中文文档)**

---

## Demos

![Trail & Particles](https://raw.githubusercontent.com/MCheng404/windhawk-mods/mouse-trail-assets/mods/mouse-trail-assets/demo1.gif)

![Centripetal Vortex](https://raw.githubusercontent.com/MCheng404/windhawk-mods/mouse-trail-assets/mods/mouse-trail-assets/demo2.gif)

![Particle Physics](https://raw.githubusercontent.com/MCheng404/windhawk-mods/mouse-trail-assets/mods/mouse-trail-assets/demo3.gif)

---

## Rendering Architecture

- **Native D3D11 Pipeline:** Custom HLSL vertex/pixel shaders with instanced particle rendering. Core trail, particle, and shape rendering does not depend on D2D1.
- **DirectComposition Hardware Overlay:** Per-pixel alpha via premultiplied DXGI flip swap chain. Tear-free composition with the desktop compositor.
- **2.5D Depth Effect:** Every particle carries a z-depth with perspective projection, simple lighting, and depth-based scaling (near = larger/brighter, far = smaller/dimmer).
- **Dual-Thread Design:** UI thread runs the window message pump; render thread owns all D3D11/DComp work. Mouse input is never blocked by rendering.
- **HDR Auto-Detection:** Detects HDR displays and uses R16G16B16A16_FLOAT automatically; falls back to SDR BGRA8.
- **Device Loss Recovery:** Proactive `GetDeviceRemovedReason()` polling + `WM_POWERBROADCAST` wake handling. Full D3D/DComp stack rebuilds on GPU TDR, driver updates, GPU switches, or sleep/resume — no black frames.
- **Display Change Handling:** Auto-resizes and repositions the overlay on monitor changes or resolution switches.
- **Additive Blend Glow:** Trail, particle, and ripple glow layers use SrcAlpha + One additive blending for translucent halos.
- **Fast-Move Interpolation:** When frame interval is ≤10ms, particles are interpolated along the cursor path to eliminate gaps during fast movement.

## Trail Render Styles (10 modes)

- **Tapered Ribbon:** Classic fading ribbon with glow, shadow, and head highlight
- **Dot Chain:** Beads along the path with configurable density and size
- **Function Curve:** Custom mathematical function deforms the trail (sine, damped, heartbeat, swirl, or user-defined formula)
- **Wave Curve:** Animated sinusoidal wave deformation
- **Shape Trail:** Spawns 9 selectable shapes (heart, star, hexagon, circle, diamond, triangle, flower, pentagon, hexagram, or random) along the path with random velocity, rotation, gravity, and configurable interval/size/count/lifetime
- **Double Line:** Two parallel trail ribbons
- **Dashed:** Constant-width segmented dashed line
- **Spiral:** Spiral deformation along the cursor path
- **Lightning:** Thin bright jagged main line with 35% probability random branch forks
- **Feather:** Thin central shaft with angled side barbs and natural feather curvature

## Color Modes (23 modes)

Single / Flowing Gradient / Rainbow Flow / Warm Flow / Cool Flow / Neon Pulse / Velocity Color / Stripes / Fire / Aurora / Cursor Extract / Cursor Mix / Metallic Gold / Cyberpunk / Pastel / Hue Rotate / Dual Pulse / Sparkle / Heatmap / Phase Interference / Spectrum Split / Grain Jitter / Gradient Warp

## Gradient System

- **Unlimited Colors:** Add any number of gradient colors (up to 16)
- **OKLab Perceptual Interpolation:** No gray midpoints, perceptually uniform color transitions
- **256-Color LUT:** Precomputed lookup table for zero-per-frame allocation
- **Flowing Gradient:** Gradient animates along the trail over time

## Cursor Color Shift (6 modes)

Off / Complementary (180°) / Analogous (30°) / Triadic (120°) / Split Complement (150°) / Custom Angle

## Visual Effects

- **Bezier Smoothing:** Catmull-Rom spline interpolation for buttery-smooth curves
- **Motion Blur:** History frame overlay with decreasing opacity (1–5 strength)
- **Enhanced Glow:** Dual-layer halo (outer glow + inner bloom) with independent toggles
- **Head Highlight + Trail Shadow:** Premium depth cues
- **Speed-Reactive Width:** Trail widens when moving fast

## Particle Physics System

The mod ships a full Newtonian particle physics engine with independent toggles for every force:

- **Particle Mass:** Each particle gets a random mass (Box-Muller normal distribution). Mass affects inertia (heavy = less drag, retains velocity), size (∝ mass^(1/3)), lifetime, and acceleration (a = F/m)
- **Particle Gravity:** Newton's law of universal gravitation F = G·m₁·m₂/r² with Plummer softening. Supports 2-body (binary star) or N-body systems (2–10 dominant bodies)
- **Centripetal Vortex:** Curved mouse motion captures particles into orbiting tracks. Angular momentum conservation, Kepler velocity gradient, orbital precession, and 3D orbital inclination. Particles fly outward when motion stops. Two physical models: Rankine vortex or custom
- **Elastic Collisions:** Momentum + kinetic energy conservation with inverse-mass position correction
- **Lorentz Force:** Charged particles circle in a magnetic field (F = q·v×B). Positive charge = counterclockwise, negative = clockwise
- **Coulomb Force:** Like charges repel, opposite charges attract (F = k·q₁·q₂/r²). Activated alongside Lorentz force
- **Brownian Motion / Turbulence:** Perlin-like spatially coherent noise field (not pure random jitter). Particles in nearby space receive similar forces, creating flowing turbulence
- **Viscous Coupling:** Nearby particles (30px) drag each other's velocity, creating fluid-like cluster behavior
- **Air Drag:** Linear low-speed drag + quadratic high-speed drag. Mass-based inertia and size-based air resistance (larger particles have more drag)
- **Spin Physics:** Particles spin with rotational air damping (spin speed decays over time)
- **Springs (Cloth):** Hooke's law springs between nearby particles with axial damping and soft cutoff
- **Environmental Gravity:** Directional constant acceleration (angle + strength configurable)
- **Environmental Wind:** Horizontal wind with gusts, direction sway, and high-frequency turbulence
- **Cursor Attraction + Repulsion:** Force field around the cursor with configurable radius and falloff

## Music Reactive Framework

- **WASAPI Loopback Capture:** Real-time system audio capture (48kHz stereo)
- **FFT Frequency Analysis:** Cooley-Tukey FFT (256/512/1024/2048 points) with Hann window
- **Beat Detection:** Three methods — energy threshold, spectral flux, multi-band detection. Low-frequency weighted
- **Frequency Band Analysis:** Bass / Mid / Treble energy levels
- **BPM Estimation:** Real-time tempo estimation
- **Music-Physics Linking:** Beat → velocity pulse, bass → particle size, volume → gravity strength, beat → vortex energy, multi-band → gravity / magnetic field / thermal noise
- **Multi-Band Independent Linkage:** Bass links to gravity, mid links to magnetic field, treble links to thermal noise — all independently configurable

## Text & Emoji Particles

- Custom text released as particles (comma-separated phrases render as whole units)
- Emoji support via Segoe UI Emoji font
- Configurable font size
- Color follows the active color mode

## Click Effects

- Starburst particle burst on click (count, radius, duration configurable)
- Expanding ripple ring on click
- Both toggleable independently

## Localization

Settings UI fully localized in English, Simplified Chinese, Traditional Chinese, and Japanese.

## Performance

- **Super Performance Mode:** Removes all particle/shape caps and fast-path downgrades
- **Adaptive Backoff:** Render thread waits 1ms when active (~1000fps headroom), 16ms when idle (~60fps responsive)
- **Game Detection:** Auto-hides in fullscreen DirectX games
- **Zero CPU Idle:** Window hidden when cursor is stationary and no effects are active
- **Background Sampling:** Desktop color sampling runs on a dedicated low-priority thread

## Installation

1. Install [Windhawk](https://windhawk.net/)
2. Download `mouse-trail.wh.cpp` from this repository
3. In Windhawk, click "Create mod" → paste the code → Save

## Function Trail Variables

Available in custom function formulas: `t` (normalized 0=head, 1=tail), `d` (distance from head in pixels), `time` (seconds). Functions: sin cos exp sqrt abs. Operators: + - * / ^.

Examples: `sin(d * 0.15) * 8`, `sin(d * 0.25) * exp(0 - t * 2.5) * 10`.

## Color Format

Hex RGB, e.g. `FF0000`=red, `00FF00`=green, `0000FF`=blue, `FFD700`=gold.

## Author

Developed by [MCheng404](https://github.com/MCheng404).

Original overlay/smear architecture inspired by [TheatriChris](https://github.com/TheatriChris)'s cursor-motion-blur mod (MIT licensed).

---

<a id="中文文档"></a>

# 中文文档

Windhawk 高度可定制鼠标拖尾特效模组。基于原生 D3D11 + DirectComposition 硬件加速，包含 23 种颜色模式、10 种拖尾渲染风格、9 种粒子形状、完整牛顿粒子物理系统（质量、引力、碰撞、电磁力、湍流、流体耦合）、向心力漩涡轨道捕获、音乐响应音频物理、2.5D 深度效果、点击特效和文字/Emoji 粒子。独立 Tool Mod 进程运行——闲置零 CPU，激活时全硬件加速。

## 渲染架构

- **原生 D3D11 管线：** 自定义 HLSL 顶点/像素着色器，粒子实例化渲染。核心拖尾、粒子、形状渲染不依赖 D2D1。
- **DirectComposition 硬件覆盖层：** 预乘 alpha DXGI 翻转交换链，per-pixel alpha，与桌面合成器无撕裂合成。
- **2.5D 深度效果：** 每个粒子带 z 深度 + 透视投影 + 简单光照 + 深度缩放（近大远小近亮远暗）。
- **双线程设计：** UI 线程跑窗口消息泵，渲染线程独占 D3D11/DComp。鼠标输入永不被渲染阻塞。
- **HDR 自动检测：** 自动识别 HDR 显示器并使用 R16G16B16A16_FLOAT，SDR 自动回退 BGRA8。
- **设备丢失恢复：** 主动 `GetDeviceRemovedReason()` 轮询 + `WM_POWERBROADCAST` 唤醒处理。GPU TDR、驱动更新、显卡切换或睡眠唤醒时全自动重建 D3D/DComp 栈——无黑帧。
- **显示变化处理：** 显示器切换或分辨率变化时自动调整覆盖层。
- **加法混合发光：** 拖尾、粒子、波纹发光层使用 SrcAlpha+One 加法混合，光晕更通透。
- **快速移动插值：** 帧间隔 ≤10ms 时沿路径插值补粒子，消除快速移动缝隙。

## 拖尾渲染风格（10种）

- **锥形飘带：** 经典渐隐飘带，带发光、阴影和头部高光
- **圆点链：** 沿路径排列的圆点，密度和大小可调
- **函数曲线：** 自定义数学函数变形轨迹（正弦、阻尼、心跳、漩涡或自定义公式）
- **波浪曲线：** 动态正弦波变形
- **形状拖尾：** 沿路径生成 9 种可选形状（爱心、五角星、六边形、圆形、菱形、三角形、花朵、五边形、六芒星或随机），带随机速度、旋转、重力，间隔/大小/数量/存活时间可调
- **双线拖尾：** 两条平行拖尾带
- **虚线拖尾：** 常量宽度分段虚线
- **螺旋拖尾：** 沿路径螺旋变形
- **闪电拖尾：** 细亮锯齿主线 + 35% 概率随机分支
- **羽毛拖尾：** 细主轴 + 两侧斜向羽枝，自然羽毛弧度

## 颜色模式（23种）

单色 / 流动渐变 / 彩虹流动 / 暖色调流动 / 冷色调流动 / 霓虹脉冲 / 速度变色 / 流动条纹 / 火焰 / 极光 / 光标取色 / 光标混色 / 金属金 / 赛博朋克 / 粉彩 / 色相旋转 / 双色脉冲 / 星光闪烁 / 热力图 / 波纹干涉 / 色谱分裂 / 颗粒抖动 / 渐变扭曲

## 粒子物理系统

模组内置完整牛顿粒子物理引擎，每个力都有独立开关：

- **粒子质量：** 每个粒子有随机质量（Box-Muller 正态分布）。影响惯性、大小、生命周期和加速度（a=F/m）
- **粒子万有引力：** 牛顿万有引力定律 F=G·m₁·m₂/r² + Plummer 软化。支持双星或 N 体系统（2–10 个主导天体）
- **向心力漩涡：** 鼠标做曲线运动时粒子被捕获到轨道上。角动量守恒 + 开普勒速度梯度 + 轨道进动 + 3D 轨道倾角
- **弹性碰撞：** 动量 + 动能守恒，按质量反比位置修正
- **洛伦兹力：** 带电粒子在磁场中做圆周运动（F=q·v×B）
- **库仑力：** 同号电荷相斥，异号电荷相吸
- **布朗运动/湍流：** Perlin-like 空间连贯噪声场，产生流动感湍流
- **粘性耦合：** 邻近粒子互相拖拽速度，产生流体般的团簇行为
- **空气阻力：** 低速线性阻力 + 高速二次阻力，质量惯性 + 大小空气阻力
- **自旋物理：** 粒子自旋带旋转空气阻尼
- **弹簧（布料）：** 邻近粒子间胡克定律弹簧 + 沿连线阻尼
- **环境重力/环境风：** 方向恒定加速度 + 阵风/摆动湍流
- **光标吸引+排斥：** 光标周围力场

## 音乐响应框架

WASAPI 回环捕获 + Cooley-Tukey FFT + 三种节拍检测 + 多频段分析 + BPM 估计 + 音乐物理联动（低频→引力、中频→磁场、高频→热噪声）。

## 安装

1. 安装 [Windhawk](https://windhawk.net/)
2. 下载 `mouse-trail.wh.cpp`
3. 在 Windhawk 中点击"创建 mod"→ 粘贴代码 → 保存

## 函数轨迹变量

自定义公式中可使用：`t`（归一化位置 0=头 1=尾）、`d`（距头部像素距离）、`time`（秒）。函数：sin cos exp sqrt abs。运算符：+ - * / ^。

## 作者

开发者 [MCheng404](https://github.com/MCheng404)。

原始覆盖层/拖尾架构灵感来自 [TheatriChris](https://github.com/TheatriChris) 的 cursor-motion-blur mod（MIT 许可证）。