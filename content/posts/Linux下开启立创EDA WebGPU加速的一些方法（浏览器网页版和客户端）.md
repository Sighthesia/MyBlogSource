---
title: "Linux下开启立创EDA WebGPU加速的一些方法（浏览器网页版和客户端）"
date: 2026-03-15T13:00:53+08:00
draft: false
description: ""
tags: ["Linux","硬件"]
---
## 前情
最近看到了关于立创EDA开启GPU加速的B站专栏，评论区中`Chucaaa_`网友提到了Linux下开启WebGPU加速的方法，我分别在N卡(3050Ti Laptop)和AMD核显(6800H，Radeon 680M)的机器上测试了，均能正常加速。

![评论](/attachments/Screenshot_2026-03-15-13-22-39.png)

## 方法

### 前提
首先需要确保已经安装了支持Vulkan的GPU驱动。如果没有安装，可以先根据不同显卡和发行版，参考不同的教程或询问AI先安装好。

需要添加两个启动参数：
1. `--enable-features=Vulkan`：将 Chromium/Electron 的底层图形绘制接口从老旧的 OpenGL 强制切换为现代的 Vulkan API。如果没有这个参数，WebGPU 可能会退回到软件渲染，CPU占用率可能会更高，运行起来更卡（我使用6800H测试中是直接卡死了）。
2. `--enable-unsafe-webgpu`：启用 WebGPU API接口（目前WebGPU在Linux下仍处于实验阶段，所以是需要手动开启的），让网页直接调用系统的 Vulkan 驱动进行 2D/3D 加速。

### 客户端
1. 先在终端中加入参数测试是否有效：

    ```bash
    lceda-pro --enable-features=Vulkan --enable-unsafe-webgpu
    ```

2. 如果有效，可以复制一份立创EDA客户端的`.desktop`文件（`/usr/share/applications/lceda-pro.desktop`）到`~/.local/share/applications`目录下，并在启动参数（`Exec`）行中追加 `--enable-features=Vulkan --enable-unsafe-webgpu`，如：

    ```bash
    Exec=lceda-pro %f --no-sandbox --enable-features=Vulkan --enable-unsafe-webgpu
    ```

    这样通过桌面快捷方式或启动器打开EDA客户端时，就能直接开启加速。

### 浏览器网页版

- 如果客户端能加速还是优先使用客户端的，经过测试客户端性能更好一些。

#### Chromium系浏览器（Chrome、Edge）
如上面所说，`--enable-features=Vulkan`可以将 Chromium/Electron 的底层图形绘制接口切换为Vulkan API，所以也可以参考上面的方法，通过加入`--enable-features=Vulkan --enable-unsafe-webgpu`参数来启动浏览器，可以直接开启WebGPU加速。

#### Firefox
Firefox还需要开启WebGPU的隐藏设置：
1. 地址栏输入 `about:config` 进入隐藏设置界面。
2. 搜索并将以下选项改为 `true`：
   - `dom.webgpu.enabled` = `true` (开启 WebGPU)
   - `gfx.webrender.all` = `true` (开启完整 WebRender)
   - `gfx.webgpu.ignore-blocklist` = `true` (绕过 Linux 黑名单限制)
3. 然后参照上面的方法，添加`--enable-features=Vulkan --enable-unsafe-webgpu`参数来启动 Firefox，即可开启 WebGPU 加速（不过貌似在开启隐藏设置后，仅使用`--enable-features=Vulkan`就能开启加速）。

## 后记
这篇写到最后貌似像是开启WebGPU的教程了。这个问题从我用Arch Linux到现在困扰我一年了，立创EDA确实是我较高强度使用的软件，画板的时候卡真的很影响效率。

