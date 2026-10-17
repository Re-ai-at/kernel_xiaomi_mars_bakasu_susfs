我的环境变量：

/home/builder/clang/bin:clang/bin:/home/builder/kernel/clang/bin:/home/builder/kernel/clang/bin:/home/builder/kernel/clang/bin:/home/builder/kernel/clang/bin:/home/builder/kernel/clang/bin:/home/builder/kernel/clang/bin:/home/builder/kernel/../clang/bin:/home/builder/kernel/../clang/bin:/home/builder/kernel/../clang/bin:/home/builder/kernel/../clang/bin:/home/builder/kernel/../clang/bin:/home/builder/kernel/../clang/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

上游仓库：https://github.com/LineageOS/android_kernel_xiaomi_sm8550

los版本：lineage-23.2-20261014

编译工具：clang version 21.0.0 + Ubuntu 22.04（wsl），其余工具：sudo apt-get install -y bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs \
gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev libelf-dev liblz4-tool \
libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync \
schedtool squashfs-tools xsltproc zip zlib1g-dev 获得。

还需要一个defconfig：scripts/extract-ikconfig boot.img > device_config 获得官方lineage-23.2-20261014编译配置


make编译代码：
make O=out ARCH=arm64 CC=clang LLVM=1 CLANG_TRIPLE=aarch64-linux-gnu- CROSS_COMPILE=aarch64-linux-gnu- -j$(nproc --all)2>&1 | tee build.log

从而获得编译后的文件Image

由此开始装包

使用boot.img 解包 获得 kernel 和 ramdisk 并附带 原boot组装时的参数

命令：mkbootimg/unpack_bootimg.py --boot_img boot.img --out boot_info


如：boot magic: ANDROID!
kernel_size: 39428608
ramdisk size: 1489270
os version: 16.0.0
os patch level: 2026-10
boot image header version: 3
command line args:

合包：

python3 ~/mkbootimg/mkbootimg.py \
  --kernel /home/builder/kernel/out/arch/arm64/boot/Image \
  --ramdisk boot_unpack/ramdisk \
  --pagesize 4096 \
  --header_version 3 \
  --os_version 16.0.0 \
  --os_patch_level 2026-10 \
  -o boot_new.img

至此完成编译所有编译。
