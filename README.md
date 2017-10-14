# README

Recently I try to use webcam on DE1 SoC board, but the offical image can not support it , so I have to recompile the kernel, but when I load the uvcvideo driver, and will get errors like:

```
videobuf2_core: Unknown symbol dma_buf_put (err 0)
videobuf2_core: Unknown symbol dma_buf_fd (err 0)
videobuf2_vmalloc: Unknown symbol dma_buf_vmap (err 0)
videobuf2_vmalloc: Unknown symbol dma_buf_vunmap (err 0)
```

Seemly the offical kernel has some bug and finally I got a patch for this issue from this git address: [git://linuxtv.org/media_build.git](git://linuxtv.org/media_build.git). I note all the steps that I take, maybe it could help you.

#### 1. Download system image

I just download system image file from this [link](http://mail.terasic.com.tw/~jim/de1_soc/DE1_SoC_OpenCL_VNC_SDL.zip).

* Linaro 12.11
* GCC 4.6
* Kernel 3.18.0

#### 2. Flash system image to SD card

Use Win32diskImager tool on Windows to flash system image to SD card.

#### 3. Boot system and compile kernel driver 

I just compile the kernel on the DE1 SoC board, but not use cross compiler.

```shell
sudo -s
apt-get install u-boot-tools
apt-get install libncurses5-dev
cd ~
git clone https://github.com/YaoQ/UVC-for-DE1-SoC.git
cd UVC-for-DE1-SoC
cp config /usr/src/3.18.0/.config
cp dma-buf.h /usr/src/3.18.0/include/linux/    # Update the dma-buf.h for fixing 
cd /usr/src/3.18.0
make menuconfig # Check the UVC driver is enabled.
make uImage LOADADDR=0x8000 -j4
make modules
make install
make modules_install
cp arch/arm/boot/uImage /media/FAT_VOLUME
reboot
modprobe uvcvideo   #Load UVC driver to check 
```

### Ref

1. http://nlug.ml1.co.uk/2013/02/gentoo-kernel-3-7-9-webcams-v4l-uvc-video-kernel-config/3965
2. http://www.alterawiki.com/wiki/Compiling_u-boot_and_Linux_Kernel_for_Cyclone_V_SoC
3. http://blog.csdn.net/flfihpv259/article/details/52438811