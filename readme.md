# firefly-3588Q-ubuntu20镜像导出

1. 在firefly板卡中安装`fireflydev`。

   ```bash
   sudo apt update
   sudo apt install fireflydev
   ```

2. 在firefly板卡接上容量较大的移动硬盘（空间大于30G），使用`ff_export_rootfs`导出根文件系统。

   ```bash
   sudo ff_export_rootfs /mdedia/firefly/your_dev/your_path
   ```

3. 将生成的文件重命名为`rootfs.img`。

4. 在另一台PC上下载打包工具`firefly-linux-repack`。

5. 从[官网](https://www.t-firefly.com/doc/download/181.html)下载固件，解压后名称改成`update.img`，放到`firefly-linux-repack`路径下，执行解包脚本`./unpack.sh`，（注：RK3588需要**RKImageMaker**使用v2.0以上版本）。

   ```bash
   # 版本过低会报错：
   $ ./unpack.sh 
   start to unpack update.img...
   ********RKImageMaker ver 1.66********
   Unpacking image, please wait...
   Error:Check update.img failed!
   Press any key to quit:
   ```

   正常输出如下：

   ```bash
   $ ./unpack.sh
   start to unpack update.img...
   ********rkImageMaker ver 2.0********
   Unpacking image, please wait...
   Exporting boot.bin
   Exporting firmware.img
   Unpacking image success.
   Android Firmware Package Tool v2.0
   Check file... OK
   ------- UNPACK ------
   package-file	offset=0x800	size=0xF4
   Image/MiniLoaderAll.bin	offset=0x1000	size=0x721C0
   Image/parameter.txt	offset=0x73800	size=0x1D6
   Image/uboot.img	offset=0x74000	size=0x800000
   Image/misc.img	offset=0x874000	size=0xC000
   Image/boot.img	offset=0x880000	size=0x10000000
   Image/recovery.img	offset=0x10880000	size=0x4A98000
   Image/rootfs.img	offset=0x15318000	size=0xF3D67000
   Unpack firmware OK!
   ------ OK ------
   Unpacking update.img OK.
   Press any key to quit:
   ```

- 解包完成后，`output/Image`目录下会生成以下文件：

  ![image-20240319141421612](https://raw.githubusercontent.com/Y-pandaman/typora-source/master/202403191414920.png)

6. 将步骤3生成的`rootfs.img`替换掉`output/Image/rootfs.img`，然后执行打包程序`./pack.sh`。

   ```bash
   $ ./pack.sh 
   start to make update.img...
   Resize rootfs partition
   dumpe2fs 1.44.1 (24-Mar-2018)
   Android Firmware Package Tool v2.0
   ------ PACKAGE ------
   Add file: ./package-file
   package-file,Add file: ./package-file done,offset=0x800,size=0xf4,userspace=0x1
   Add file: ./Image/MiniLoaderAll.bin
   bootloader,Add file: ./Image/MiniLoaderAll.bin done,offset=0x1000,size=0x721c0,userspace=0xe5
   Add file: ./Image/parameter.txt
   parameter,Add file: ./Image/parameter.txt done,offset=0x73800,size=0x1e0,userspace=0x1
   Add file: ./Image/uboot.img
   uboot,Add file: ./Image/uboot.img done,offset=0x74000,size=0x800000,userspace=0x1000
   Add file: ./Image/misc.img
   misc,Add file: ./Image/misc.img done,offset=0x874000,size=0xc000,userspace=0x18
   Add file: ./Image/boot.img
   boot,Add file: ./Image/boot.img done,offset=0x880000,size=0x10000000,userspace=0x20000
   Add file: ./Image/recovery.img
   recovery,Add file: ./Image/recovery.img done,offset=0x10880000,size=0x4a98000,userspace=0x9530
   Add file: ./Image/rootfs.img
   rootfs,Add file: ./Image/rootfs.img done,offset=0x15318000,size=0x36442c000,userspace=0x6c8858
   Add CRC...
   Make firmware OK!
   ------ OK ------
   ********rkImageMaker ver 2.0********
   Generating new image, please wait...
   Writing head info...
   Writing boot file...
   Writing firmware...
   Generating MD5 data...
   MD5 data generated successfully!
   New image generated successfully!
   Making update.img OK.
   /media/bdca/pandaman/firefly-rk3399-linux-repack
   Press any key to quit:
   ```

7. 完成之后会在`firefly-linux-repack`目录下生成`new_update.img`，即为可刷机镜像。

注：

- 如果生成的镜像无法给新板卡刷机，可能是因为镜像太大了，删除部分无用的软件/库，重新生成`rootfs.img`文件。
