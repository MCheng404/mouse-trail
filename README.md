# Mouse Trail

A highly customizable mouse cursor trail mod for [Windhawk](https://windhawk.net/). Built on native D3D11 + DirectComposition hardware acceleration, featuring 23 color modes, 10 trail render styles, 9 particle shapes, a full Newtonian particle physics system (mass, gravity, collisions, electromagnetic forces, turbulence, fluid coupling), centripetal vortex orbital capture, music-reactive audio physics, 2.5D depth effects, click effects, and text/emoji particles. Runs as an isolated Tool Mod process — zero CPU when idle, full hardware acceleration when active.

**[中文文档](README.zh-CN.md)**

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