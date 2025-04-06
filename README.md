### File list
* /usr/src/sys/arch/arm/dts/imx6q-marsboard.dts
* /usr/src/sys/arch/arm/nxp/imx6_clk.c (from netbsd-riotboard)
* /usr/src/sys/arch/arm/nxp/imx6_iomux.c (from netbsd-riotboard)

### Boot log
https://dmesgd.nycbug.org/dmesgd?do=view&id=8177

### Build kernel
./build.sh -U -u -O /usr/builds/obj.earmv7hf -T /usr/builds/tool.earmv7hf -j2 -m evbarm -a earmv7hf kernel=GENERIC

### Write image
dd if=armv7.img of=/dev/rld0d bs=1m conv=sync

### Two boot methods without breaking the builtin U-Boot
cd /usr/pkgsrc/sysutils/u-boot-marsboard ; make install

#### use u-boot.bin
* mount /dev/rld0e /mnt
* cp /usr/pkg/share/u-boot/marsboard/u-boot.bin /mnt

=> mmc dev 0

=> fatload mmc 0:1 0x17800000 u-boot.bin

=> go 0x17800000

#### or u-boot.imx

* dd if=/usr/pkg/share/u-boot/marsboard/u-boot.imx of=/dev/rld0d bs=1k seek=1 conv=sync

=> mw.l 0x020d8040 0x2840; mw.l 0x020d8044 0x10000000; reset

ref. https://fedevel.com/forum/imx6-rex-tiny-rex-open-rex/13026-force-boot-from-sd-card

#### then

=> fatload mmc 0:1 0x13000000 dtb/imx6q-marsboard.dtb

=> fatload mmc 0:1 0x12000000 efi/boot/bootarm.efi

=> bootefi 0x12000000 0x13000000

