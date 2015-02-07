# pcDuino3Nano-u-boot
pcDuino3 Nano u-boot build scripts and auxiliary files

Manual u-boot build Process
---------------------------
mkdir staging
cd staging/

git clone https://github.com/RobertCNelson/u-boot.git

cp ../../github/lib/config/Linksprite_pcDuino3_Nano_defconfig ./configs

cp ../../github/lib/config/sun7i-a20-pcduino3-nano.dts ./arch/arm/dts

make -s CROSS_COMPILE=arm-linux-gnueabihf- clean

make -j2 Linksprite_pcDuino3_Nano_defconfig CROSS_COMPILE=arm-linux-gnueabihf-

For now apply patch at: http://lists.denx.de/pipermail/u-boot/2015-February/203845.html

echo "CONFIG_ARMV7_BOOT_SEC_DEFAULT=y" >> .config
echo "CONFIG_ARMV7_BOOT_SEC_DEFAULT=y" >> ./spl/.config
echo "CONFIG_OLD_SUNXI_KERNEL_COMPAT=y" >> .config
echo "CONFIG_OLD_SUNXI_KERNEL_COMPAT=y" >> ./spl/.config

make -j2 CROSS_COMPILE=arm-linux-gnueabihf-

\# Copy u-boot with spl to staging area

mkdir -p ../output/u-boot
cp u-boot-sunxi-with-spl.bin ../output/u-boot

\# Initialize sdcard and write u-boot with spl to it

sudo dd if=/dev/zero of=/dev/sdb bs=1M count=1
sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdb bs=1024 seek=8
sync
