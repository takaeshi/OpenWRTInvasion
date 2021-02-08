# Root shell exploit for several Xiaomi routers: miWifi 3C/mi router 3c

## How to run

**NOTE: FROM VERSION `0.0.2` THE ROUTER NEEDS INTERNET ACCESS**. If you require to run the exploit without internet access please try version `0.0.1`. Find the versions here: https://github.com/acecilia/OpenWRTInvasion/releases

```shell
pip3 install -r requirements.txt # Install requirements
python3 remote_command_execution_vulnerability.py # Run the script
```

You will be asked for the router IP address and for the `stok`. You can grab the `stok` from the router URL after you log in to the admin interface:

![](readme/readme-001.png)

After that, a telnet server will be up and running on the router. You can connect to it by running:

```
telnet <router_ip_address>
```

* User: `root`
* Password: `root`

The script also starts an ftp server at port 21, so you can get access to the filesystem using a GUI (for example [cyberduck](https://cyberduck.io)).

## Supported firmware versions

* MiWifi 3C: works on firmware versions `2.9.217`, `2.14.45` and `2.8.51_INT`: [OpenWrt forum](https://forum.openwrt.org/t/support-for-xiaomi-miwifi-3c/11643/23), [OpenWrt forum](https://forum.openwrt.org/t/support-for-xiaomi-miwifi-3c/11643/17).

## Xiaomi Miwifi 3c
### Firmwares

This repository contains the following firmwares:

* Official Xiaomi - `2.14.45` - in Chinese. 
  * URL in this repository: https://github.com/takaeshi/mi-router-3c-OpenWRT/raw/master/firmwares/stock/miwifi_r3l_firmware_0b49f_2.14.45.bin
* Official Xiaomi - `2.8.51_INT` - in English. 
  * URL in this repository: https://github.com/takaeshi/mi-router-3c-OpenWRT/raw/master/firmwares/stock/miwifi_r3l_all_23e37_2.8.51_INT.bin
 
If you have a pending update in your Xiaomi stock firmware, you can check its md5 hash and the download url by navigating to:

```
http://192.168.31.1/cgi-bin/luci/;stok=<stok>/api/xqsystem/check_rom_update
```

### Install breed
 Installation instructions via SSH: 
 
 ```
 wget -P /tmp  https://breed.hackpascal.net/breed-mt7688-reset38.bin

 mtd_write write /tmp/breed-mt7688-reset38.bin Bootloader
```

When installing OpenWrt on the Xiaomi 4A Gigabit, there are several options. **Note that there isn't a stable release for it yet, which means that the firmware may be unstable**:

* One of the images listed in the [official OpenWrt wiki page](https://openwrt.org/inbox/toh/xiaomi/xiaomi_mi_router_4a_gigabit_edition)

* Build your own image with `imagebuilder`, using the latest source code on `master`:

  ```
  docker pull openwrtorg/imagebuilder:ramips-mt7621-master
  docker run --rm -v "$(pwd)"/bin/:/home/build/openwrt/bin -it openwrtorg/imagebuilder:ramips-mt7621-master
  make PROFILE=xiaomi_mir3g-v2 image
  ```

* Wait until there is a stable release of OpenWrt

If **after reading above text** you still want to proceed, after login to the router through telnet run the following commands:

```shell
cd /tmp
curl https://raw.githubusercontent.com/acecilia/OpenWRTInvasion/master/firmwares/OpenWrt/06-06-2020/openwrt-ramips-mt7621-xiaomi_mir3g-v2-squashfs-sysupgrade.bin --output firmware.bin # Put here the URL you want to use to download the firmware
./busybox sha256sum firmware.bin # Verify the firmware checksum before flashing, very important to avoid bricking your device!
mtd -e OS1 -r write firmware.bin OS1 # Install OpenWrt
```

This will install the snapshot version of OpenWrt (without Luci). You can now use ssh to connect to the router (and install Luci if you prefer it).

### Performance:

Some users have reported worse WIFI performance in OpenWrt than in the stock firmware. See the following links:

* [OpenWrt forum](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-but-requires-overwriting-spi-flash-with-programmer/36685/430)
* [OpenWrt forum](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-but-requires-overwriting-spi-flash-with-programmer/36685/431)
* [OpenWrt forum](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-but-requires-overwriting-spi-flash-with-programmer/36685/451)

## For more info and support go to:

* [OpenWrt forum thread](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-but-requires-overwriting-spi-flash-with-programmer/36685)
* [Slack workspace](https://join.slack.com/t/openwrt-workspace/shared_invite/zt-cz2m5uf4-Q8wbP_LKggOy9B7IQyaqfA)

## If you brick your device

You can find solutions in the following links:

* User [albertcp](https://forum.openwrt.org/u/albertcp) posted a very detailed guide: [OpenWrt forum](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-but-requires-overwriting-spi-flash-with-programmer/36685/402)
* User [micky0867](https://forum.openwrt.org/u/micky0867) has some more comments about the topic: [OpenWrt forum](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-but-requires-overwriting-spi-flash-with-programmer/36685/391)
* User [hoddy](https://forum.openwrt.org/u/hoddy) created a [video tutorial](https://www.youtube.com/watch?v=VxzEvdDWU_s)

## Acknowledgments

* Original vulnerabilities and exploit: [UltramanGaia](https://github.com/UltramanGaia/Xiaomi_Mi_WiFi_R3G_Vulnerability_POC)
* Instructions to install OpenWrt after exploit execution: [rogerpueyo](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-but-requires-overwriting-spi-flash-with-programmer/36685/21)
* Testing and detailed install instructions: [hey07](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-but-requires-overwriting-spi-flash-with-programmer/36685/349)
* Checking the URL of pending updates: [sicklesareterrible](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-and-flashable-with-openwrtinvasion/36685/1114?u=acecilia)

## Demo

### Version 0.0.2 and higher: telnet

![Alt Text](readme/exploit-002.gif)

### Version 0.0.1: netcat (legacy)

![Alt Text](readme/exploit-001.gif)
