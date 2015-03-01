# pcDuino3Nano-u-boot
pcDuino3 Nano u-boot build script and auxiliary files

Manual u-boot build Process
---------------------------
mkdir staging  
cd staging/

git clone https://github.com/digitalhack/pcDuino3Nano-u-boot.git  
git clone https://github.com/RobertCNelson/u-boot.git  

cd ./u-boot

cp ../pcDuino3Nano-u-boot/Linksprite_pcDuino3_Nano_defconfig ./configs

cp ../pcDuino3Nano-u-boot/sun7i-a20-pcduino3-nano.dts ./arch/arm/dts

make -s CROSS_COMPILE=arm-linux-gnueabihf- clean

make -j2 Linksprite_pcDuino3_Nano_defconfig CROSS_COMPILE=arm-linux-gnueabihf-

\# Until included in mainline apply patch at:  
\# &nbsp;&nbsp;&nbsp;&nbsp;http://lists.denx.de/pipermail/u-boot/2015-February/203845.html


echo "CONFIG_ARMV7_BOOT_SEC_DEFAULT=y" >> .config  
echo "CONFIG_ARMV7_BOOT_SEC_DEFAULT=y" >> ./spl/.config  
echo "CONFIG_OLD_SUNXI_KERNEL_COMPAT=y" >> .config  
echo "CONFIG_OLD_SUNXI_KERNEL_COMPAT=y" >> ./spl/.config  


make -j2 CROSS_COMPILE=arm-linux-gnueabihf-

\# u-boot with is in u-boot-sunxi-with-spl.bin   
\# u-boot is in u-boot.img  
\# sunxi-spl.bin is in ./spl/sunxi-spl.bin  

\# To copy u-boot with spl to staging area

mkdir -p ../output/u-boot  
cp u-boot-sunxi-with-spl.bin ../output/u-boot  
cp spl/sunxi-spl.bin ../output/u-boot  
cp u-boot.img ../output/u-boot  

\# If you wish to update u-boot continue below.  I you are building a  
\# complete sdcard continue on to digitalhack/pcDuino3Nano-kernel

\# Update with you device  
card="/dev/sdb"  

\# To initialize sdcard or zero out old u-boot

sudo dd if=/dev/zero of=${card} bs=1M count=1  

or to keep the partition table  

sudo dd if=/dev/zero of=${card} bs=1k count=1023 seek=1

\# To write u-boot with spl to it

sudo dd if=u-boot-sunxi-with-spl.bin of=${card} bs=1024 seek=8  
sync  

\# To write u-boot and sunxi spl to it

dd if=spl/sunxi-spl.bin of=${card} bs=1024 seek=8  
dd if=u-boot.img of=${card} bs=1024 seek=40  
