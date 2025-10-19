---
title: "使用pcman及aur助手更新时忽略或暂时忽略软件包方法摘要"
date: 2025-10-12T22:48:57+08:00
draft: false
description: ""
tags: ["Linux"]
---
⚠️ 来自 Arch Linux Wiki 的提示：
> 警告：
在跳过软件包时要小心，因为部分升级不受支持

- 不过如果出现了依赖冲突，pacman 和 aur 助手会报错，通常不会导致严重后果
## 暂时忽略
在命令行输入命令更新软件时暂时忽略某些包的更新，仅对本次更新有效

### 方法
附加 `--ignore` 关键字，多个包名间用**逗号**分隔

### 示例
使用 paru 更新系统，并忽略 nvidia 相关软件包
```shell
paru -Syu --ignore nvidia-dkms,nvidia-utils,opencl-nvidia,lib32-nvidia-utils
```

## 永久忽略
在命令行输入命令更新软件使始终忽略某些包的更新

### 方法
 编辑 `/etc/pacman.conf`，取消 `[options]` 部分 `IgnorePkg=` 行的注释，并附加要忽略的软件包名，多个包名间使用**空格**分隔

### 示例
 ```
 [options]
 ......
 Ignore= nvidia-dkms nvidia-utils opencl-nvidia lib32-nvidia-utils
 ```
 - 该情况下仍可手动指定包名对被永久忽略的软件进行**暂时更新**，如
	```shell
	paru -S nvidia-dkms
	```
	此时会出现信息，提示是否更新被忽略的包，输入 `y` 或回车即可更新

## 附加：永久忽略软件组
忽略整个软件组，如 gnome 或 kde 的软件套件的更新，通常比较少使用
- 提示：使用 `pacman -Sg` 可以获取系统安装的所有软件组

### 方法
同上述 *永久忽略* 部分的方法，区别是将要忽略的软件组附加到 `[options]` 部分 `IgnoreGroup=` 后

## 后记
- 摘自：[Arch Linux Wiki - pacman](https://wiki.archlinuxcn.org/wiki/Pacman#%E5%9C%A8%E5%8D%87%E7%BA%A7%E6%97%B6%E8%B7%B3%E8%BF%87%E8%BD%AF%E4%BB%B6%E5%8C%85)
- 参考：[Github - How can I ignore one package when updating?](https://github.com/Morganamilo/paru/discussions/813)

可以猜猜我为什么要忽略 nvidia 的更新？😊当然是某次更新后我用的 hyprland 突然无法正常调度 n 卡了，至今未解决，so **** you nvidia 🤓
