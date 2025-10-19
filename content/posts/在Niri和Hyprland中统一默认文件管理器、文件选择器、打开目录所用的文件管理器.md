---
title: "在Niri/Hyprland中统一默认文件管理器、文件选择器、打开目录所用的文件管理器"
date: 2025-10-18T22:06:23+08:00
draft: false
description: ""
tags: ["Linux","美化"]
---
在Niri或Hyprland这样轻量的桌面合成器上，通常没有完善的桌面门户组件（实际上 Hyprland 有 xdg-desktop-potal-hyprland），或换了多个作者做的dotfile后，KDE、GNOME等组件都给装上了，打开文件夹时，各种文件管理器就开始群魔乱舞了：一会打开的是KDE的、应用QT主题的Dolphin，一会是GNOME的、应用GTK主题的Nautilus，一会是xdg-desktop-potal的、应用GTK主题的、我也不知道叫啥名的文件选择器......

总之就是非常混乱，特别是在各种主题没统一好时，突然弹出一个及其不和谐的应用看着就很烦~~(这就是为什么我用Hyprland和Niri只喜欢用终端的原因，因为离开终端，应用主题就割裂了)~~

为了统一它们，我走了不少弯路，发现不是不想要GNOME的文件管理器时pacman -Rns nautilus就完了，还需要修改三种触发文件管理器的机制：`xdg-open`、D-Bus 文件管理器接口、 `xdg-desktop-portal` 。它们分别控制不同场景下的打开文件或文件夹的行为，仅配置其中一项，无法保证所有应用行为一致。

下文我会将文件管理器统一为KDE的Dolphin，因为KDE的组件在各种Ricing的dotfile中很受欢迎，并且美观实用。

## 一、设置 MIME 类型默认应用

这是最广为人知的方法，本质和Windows下设置默认打开应用一样，目的是控制 `xdg-open` 的行为，即控制需要使用什么文件管理器打开文件夹。比如在终端里执行 `xdg-open ~/Downloads` 就是一种打开文件夹的行为，这会调用默认的文件管理器打开该文件夹。

### 方法说明
使用 `xdg-mime` 命令将 `inode/directory`（文件夹）和 `x-scheme-handler/file`（file:// 协议）关联到 Dolphin。Dolphin 的 `.desktop` 文件名通常是 `org.kde.dolphin.desktop`。

### 步骤
 1. 设置默认文件管理器
```sh
xdg-mime default org.kde.dolphin.desktop inode/directory
```
2. 设置 file:// 链接的默认打开方式（部分应用应该会用到，以防万一）
```sh
xdg-mime default org.kde.dolphin.desktop x-scheme-handler/file
```

 - 验证是否生效：运行 `xdg-mime query default inode/directory`，应返回 `org.kde.dolphin.desktop`。

---

## 二、接管 D-Bus 文件管理器接口

我遇到的问题是用Edge时点击下载的文件右边的“在文件夹中显示”时，并不调用 `xdg-open`，而是调用了D-Bus的`interface=org.freedesktop.FileManager1; member=ShowItems`。然后导致了调用的文件管理器不是我上面设置的Dolphin，而是Natuilus（好像可以通过卸载Natuilus解决？但是接管D-Bus应该是更稳健的方法）

- 可以使用下面的命令监听 D-Bus 文件管理器接口的活动，执行命令后，再重现一下打开文件管理器的行为，如果有更新的信息，说明这里调用的就是 D-Bus 文件管理器接口。

```sh
dbus-monitor --session "type='method_call',interface='org.freedesktop.FileManager1'"
```

### 方法说明
通过创建用户级 D-Bus 服务覆盖文件，强制系统使用 Dolphin 的 `--daemon` 模式来响应此 D-Bus 请求。

### 步骤
1. 创建用户 D-Bus 服务目录
```sh
mkdir -p ~/.local/share/dbus-1/services
```

2. 创建覆盖文件，指定使用 Dolphin。这里Dolphin可以换成想要的文件管理器。
```sh
cat > ~/.local/share/dbus-1/services/org.freedesktop.FileManager1.service <<EOF
[D-BUS Service]
Name=org.freedesktop.FileManager1
Exec=/usr/bin/dolphin --daemon
EOF
```

---

## 三、配置 xdg-desktop-portal 

现代应用（包括原生 Wayland 应用、Flatpak、Electron）通过 `xdg-desktop-portal` 请求文件选择对话框。系统会根据 `XDG_CURRENT_DESKTOP` 自动选择后端，但若同时安装了多个 Portal 实现（如 GTK 和 KDE），局面就开始混乱了。

> ⚠️注意：这一步是必配置的，单靠删除软件包没有用。因为`xdg-desktop-portal` 是基础的软件包，并且自带了一个文件选择器，如果不配置，默认就会使用它的文件选择器(很简洁，也不是不能用吧~)

### 方法说明
通过自定义 Portal 配置文件，显式指定 `FileChooser` 接口使用指定的后端。即使存在 `xdg-desktop-portal`，此配置也能确保优先使用想要使用的桌面环境的 Portal。

### 步骤
1. 安装想要使用的某个桌面环境的 Potal（不同的Portal仅是包名后缀不一样），如KDE：

```sh
sudo pacman -S xdg-desktop-portal-kde
```

2. 创建 `~/.config/xdg-desktop-portal/portals.conf` 文件，用于配置 Portal，并在文件中填入以下内容：
```ini
[preferred]
org.freedesktop.impl.portal.FileChooser=kde
```
其中，`kde` 可以换成其他桌面Portal的后缀，如 `gnome`、`gtk`

### 杂项
由于Portal是通过环境变量识别的，所以通常来说是通过设置环境变量指定所使用的Portal的，但是不知道为什么没有用，所以用上面的方法就可以。不过这里还是列出这两个环境变量：

```config
XDG_CURRENT_DESKTOP "niri"
XDG_SESSION_DESKTOP "kde"
```

到此，默认的文件管理器就应该完全设置为Dolphin了。

---

## 后记
最近从Hyprland叛逃到Niri了，之前在Hyprland一直使用的是end4这个作者的 illogical-impluse dofile，现在用的noctalia shell也是，KDE组件用惯了，而且KDE的应用主题是QT的，matugen这种主题色生成工具生成的颜色方案应用基本没问题。坚持统一文件管理器的主要原因是Dolphin在美观的同时，实用性也很强，~~最重要的是我是强迫症，必须要统一。~~不过我平时使用yazi更多，只不过拖动文件时需要图形界面管理器，yazi也有个拖动插件，只不过效果不是很好。

---

## 参考资料
- [Arch Wiki: XDG_MIME_Applications](https://wiki.archlinux.org/title/XDG_MIME_Applications  )
- [Arch Wiki: xdg-desktop-portal](https://wiki.archlinux.org/title/Xdg-desktop-portal  )
