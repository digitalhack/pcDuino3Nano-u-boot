# pcDuino3Nano-u-boot
pcDuino3 Nano u-boot build scripts and auxiliary files

Manual u-boot build Process
---------------------------
mkdir staging
cd staging/

git clone https://github.com/digitalhack/pcDuino3Nano-u-boot.git<br>
git clone https://github.com/RobertCNelson/u-boot.git<br>

cd ./u-boot

cp ../pcDuino3Nano-u-boot/Linksprite_pcDuino3_Nano_defconfig ./configs

cp ../pcDuino3Nano-u-boot/sun7i-a20-pcduino3-nano.dts ./arch/arm/dts

make -s CROSS_COMPILE=arm-linux-gnueabihf- clean

make -j2 Linksprite_pcDuino3_Nano_defconfig CROSS_COMPILE=arm-linux-gnueabihf-

\# Until included in mainline apply patch at: http://lists.denx.de/pipermail/u-boot/2015-February/203845.html


echo "CONFIG_ARMV7_BOOT_SEC_DEFAULT=y" >> .config<br>
echo "CONFIG_ARMV7_BOOT_SEC_DEFAULT=y" >> ./spl/.config<br>
echo "CONFIG_OLD_SUNXI_KERNEL_COMPAT=y" >> .config<br>
echo "CONFIG_OLD_SUNXI_KERNEL_COMPAT=y" >> ./spl/.config<br>


make -j2 CROSS_COMPILE=arm-linux-gnueabihf-

\# u-boot with is in u-boot-sunxi-with-spl.bin
\# u-boot is in u-boot.img
\# sunxi-spl.bin is in ./spl/sunxi-spl.bin

\# Update with you device
card="/dev/sdb"

\# Copy u-boot with spl to staging area

mkdir -p ../output/u-boot
cp u-boot-sunxi-with-spl.bin ../output/u-boot

\# Initialize sdcard and write u-boot with spl to it

sudo dd if=/dev/zero of=${card} bs=1M count=1
sudo dd if=u-boot-sunxi-with-spl.bin of=${card} bs=1024 seek=8
sync

\# To initialize sdcard and write u-boot and sunxi spl to it

dd if=spl/sunxi-spl.bin of=${card} bs=1024 seek=8
dd if=u-boot.img of=${card} bs=1024 seek=40
