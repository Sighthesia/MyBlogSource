---
title: "Arch Linux Btrfs下系统从双硬盘迁移到大容量单硬盘记录"
date: 2026-02-11T20:03:59+08:00
draft: false
description: ""
tags: ["Linux","优化"]
series: ["Btrfs优化"]
series_order: 1
---
# 前情

最开始用Arch Linux是单盘512GB，分了一大块Btrfs分区，分了`@`字卷挂载到`/`上。后来因为自己配置的Snapper出问题了，快照占了几百个GB（而且我还不知道），于是就又新增了一块512GB的，使用`sudo btrfs device add /dev/nvme1n1 /`很轻松把两块盘合并到一起了，再使用Btrfs Assistant进行了blance，均分两块硬盘数据。

最近因为固态涨价，我动了动聪明的头脑一想，这不是购入一块1TB固态来上位替换两块512GB固态的大好时机？（实际上并不是（）询问AI可行性和方法后，我就开始了这次迁移。

# 思路

- 利用Btrfs的RAID/Device特性，先把新硬盘加入到设备池中，再将旧硬盘移出设备池，数据便会自动从旧硬盘迁移到新硬盘，全程系统可正常使用，非常方便！

# 准备

1. 硬盘盒：因为我的笔记本只有两个M2硬盘位，Btrfs设备池操作需要所有硬盘同时在线，所以需要一个外置的M2硬盘盒来连接新硬盘。我选择闲鱼20元购买了**RTL9210B**硬盘盒，不仅温度比JMS583低，还支NVME和SATA两种硬盘。
2. 格式化和创建分区：新硬盘到手，删除所有分区，保证有GPT分区表，然后先创建一个fat32格式的EFI分区使其在硬盘前部，512MB即可，**否则没有办法配置引导，只能备份数据删除所有分区重来**（我可是亲身体会了;-;，不过可以用U盘创建临时的引导分区引导系统启动）。然后剩下的空间创建linux filesystem格式的分区即可，或Btrfs格式（不过加入存储池会让你重新格式化），不需要格式化，Btrfs会自动处理。
3. 删除缓存和不必要的文件以减少写入量（可选）：比如可以清理pacman缓存`sudo pacman -Scc`，清理`trash`等。
4. 准备一个U盘，安装Ventoy，并将Arch Install ISO放到U盘中（可选但建议）：万一操作错误无法启动系统，可以进入安装环境进行修复。

# 具体操作

准备好后，插入硬盘盒连接新硬盘，使用`lsblk -f`确认新硬盘设备名（无其它USB存储设备且新硬盘分好了EFI分区时为`/dev/sda2`），然后开始操作：

## 1. 迁移数据

1. 将设备加入到设备池：
    ```bash
    sudo btrfs device add /dev/sda1 /
    ```
    - 可以查看当前Btrfs设备池中的设备，显示了`/dev/sda1`就是成功了：
    ```bash
    sudo btrfs filesystem show /
    ```
2. 将两块旧硬盘移出设备池，然后数据便会迁移到设备池仅剩的硬盘上：
    ```bash
    sudo btrfs device remove /dev/nvme0n1 /dev/nvme1n1 /
    ```
    - 可能会出现下方提示，这是正常的，因为Btrfs设备移除是一个接一个进行的，出现错误也是触发了Btrfs的RAID限制，实际上仍然可以正常进行操作：
      ```bash
      WARNING: there are 2 devices for removal, this will not remove them at once
      but one by one and the remaining devices can still be written to.
      Use --force to skip the timeout.
      If this is not expected, press Ctrl-C to stop.

      10 9 8 7 6 5 4 3 2 1ERROR: error removing device '/dev/nvme0n1': Invalid argument
      ```
    这个过程会比较久，取决于数据量大小，可以使用`sudo btrfs filesystem show /`查看迁移进度，等待旧设备的`used`变为0，终端命令结束即完成，期间系统可以正常使用，中途**绝对不要断电**！

## 2. 重建引导

完成数据迁移后，先不要关机，也不要拔掉硬盘，由于引导分区还在旧硬盘上，并且fstab中挂载的EFI分区的UUID仍然还是旧硬盘的，所以关机后进不了系统，需要为新硬盘的EFI分区重建引导，并修改fstab。

- Btrfs的UUID是由文件系统格式化时生成的标识符，而迁移操作会将UUID一并迁移，所以只需要修改新创建的EFI分区的UUID即可。

1. 确认 EFI 分区 UUID：
    ```bash
    lsblk -f
    ```
    找到新硬盘的EFI分区（同上，一般是`/dev/sda1`）对应的UUID，复制下来。

2. 创建挂载点并挂载：
    ```bash
    sudo mkdir -p /boot/efi
    sudo mount /dev/sda1 /boot/efi
    ```
    - 用的是`/boot/efi`，而不是`/boot`，这样Linux内核文件（vmlinuz）仍然存储在Btrfs分区上，而EFI分区只存储很小的`.efi`引导文件，所以512MB足够了。

3. 固化到fstab，开机时自动挂载，修改`/etc/fstab`：
    找到 `/boot` 或 `/boot/efi` 的旧行，将其修改为：
    ```text
    UUID=上面复制的UUID  /boot/efi  vfat  defaults  0  2
    ```

4. 安装Grub：
    ```bash
    sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ArchLinux --recheck
    ```

5.  生成Grub配置文件：
    ```bash
    sudo grub-mkconfig -o /boot/grub/grub.cfg
    ```

# 后记
到这里没出错的话，迁移就完成了。操作全是Gemini 3教我的，太强了。

## 附录
我的Btrfs fstab的挂载配置优化，可供参考：
```shell script
# root
UUID=e1566f33-3898-4c77-beb8-d1bcf2588967 / btrfs defaults,subvol=@,noatime,compress=zstd:3,discard=async,space_cache=v2 0 1

# user
UUID=e1566f33-3898-4c77-beb8-d1bcf2588967 /home btrfs defaults,subvol=@home,noatime,compress=zstd:3,discard=async 0 2
UUID=e1566f33-3898-4c77-beb8-d1bcf2588967 /root btrfs defaults,subvol=@root,noatime,compress=zstd:3,discard=async 0 2

# intensely written directories
UUID=e1566f33-3898-4c77-beb8-d1bcf2588967 /var/log btrfs defaults,subvol=@log,noatime,compress=zstd:8,discard=async 0 2
UUID=e1566f33-3898-4c77-beb8-d1bcf2588967 /var/cache btrfs defaults,subvol=@cache,noatime,compress=zstd:8,discard=async 0 2
UUID=e1566f33-3898-4c77-beb8-d1bcf2588967 /var/tmp btrfs defaults,subvol=@tmp,noatime,compress=zstd:8,discard=async 0 2

# tmpfs
tmpfs /tmp tmpfs rw,noatime,nosuid,nodev,size=8G,mode=1777 0 0

# Mount @swap subvolume
UUID=e1566f33-3898-4c77-beb8-d1bcf2588967  /swap  btrfs  defaults,subvol=@swap,noatime,discard=async  0 0

# Enable swap file
/swap/swapfile  none  swap  defaults  0 0

# EFI
UUID=0B5D-6088 /boot/efi vfat defaults 0 2
```