# x96 mini h313 chipset
This is steps for X96 mini h313 chipset 2g/16g. 

## Requirements

Before you start, you need:

- Linux PC (Windows may work but not tested and there is a reported that windows failed fastboot boot command)
- Android Platform-tool for `adb`/`fastboot` and installed in your Linux PC.
- USB-A to USB-A cable for fastboot
- Keyboard and Mouse
- Magisk App (I use 24.3)
- Magisk safetynet fix module ()


---
## Steps
### Install Magisk App
1. Connect Kyeboard/Mouse and boot. Then connect to wifi (or ethernet)
2. Configure static IP address in your router if possible
3. Install Magisk App (24.3) (Example your device IP is 192.168.1.129)
```
adb connect 192.168.1.129
* daemon not running; starting now at tcp:5037
* daemon started successfully
connected to 192.168.1.129:5555

adb install Magisk-v24.3.apk
Success

adb reboot
```
### Extract boot.img and vbmeta.img
connect again after reboot
```
adb connect 192.168.1.129
connected to 192.168.1.129:5555
```
4. Extract boot.img in `/dev/block/by-name/` and copy to `/sdcard/Download/boot.img`
```
adb shell "echo 'dd if=/dev/block/by-name/boot of=/sdcard/Download/boot.img' | su"
65536+0 records in
65536+0 records out
33554432 bytes (32 M) copied, 0.749928 s, 43 M/s
```
5. Extract vbmeta.img in `/dev/block/by-name/` and copy to `/sdcard/Download/vbmeta.img`
```
adb shell "echo 'dd if=/dev/block/by-name/vbmeta of=/sdcard/Download/vbmeta.img' | su"
32768+0 records in
32768+0 records out
16777216 bytes (16 M) copied, 0.386947 s, 41 M/s
```
### Patch boot.img 
1. Open Magisk App
2. Press the Install button in the Magisk App
3. Choose "Select and Patch a File" in method, and select(double click) the boot.img image in Download.
4. Press Let's go, then you will see
```
- Device platform: armeabi-v7a
- Instaling: 24.3 (24300)
- Copying image to cache
- Unpacking boot image
- Checking ramdisk status
- Stock boot image detected
- Patching ramdisk
- Repacking boot image

****************************
Output file is written to
/storage/emulated/0/Download/magisk_patched-24300_xxxxx.img
****************************
- All done!
```

5. Go to Linux terminal. Copy patched image `magisk_patched-24300_xxxxx.img` and `vbmeta.img` to Linux PC
```
adb pull /sdcard/Download/magisk_patched-24300_xxxxx.img
adb pull /sdcard/Download/vbmeta.img
```
6. Connect to ATV device. Remove `su` binary from /system/xbin/ before fastboot by
```
adb -s 192.168.1.129 shell
su
mount -o rw,remount /
rm /system/xbin/su
```
7. Connect USB to most left port and Linux USB and reboot with recovery. Most left port is USB-USB, right USB connect keyboard and no Mouse connected. You will need to use Keyboard to get to Fastboot Mode after reboot. 
In Linux Termianl
```
adb -s 192.168.1.129 reboot recovery
```
8. Select `Enter fastboot` by Keyboard -> Select `Reboot to bootloader` by Keyboard. Then you will see Android with opening his berry.
9. Check device connected in Linux terminal
```
sudo fastboot devices
[sudo] password for mizu:
2c001474112607f1c4e     fastboot
```
10. Flash patched boot.img with command:
```
sudo fastboot flash boot /path-to-img/magisk_patched-24300_xxxxx.img
target reported max download size of 33554432 bytes
sending 'boot' (32768 KB)...
OKAY [  1.505s]
writing 'boot'...
OKAY [  0.605s]
finished. total time: 2.110s
```
11. Flash vbmeta.img with command:
```
sudo fastboot --disable-verity --disable-verification flash vbmeta /path-to-img/vbmeta.img
target reported max download size of 33554432 bytes
sending 'vbmeta' (16384 KB)...
OKAY [  0.759s]
writing 'vbmeta'...
OKAY [  0.331s]
finished. total time: 1.090s
```
12. Reboot with command:
```
sudo fastboot reboot
```
### Configure Magisk
1. Remove USB-USB cable and connect Mouse back
1. Open Magisk App and make sure Magisk 24.3 is installed.
2. If asked reboot, reboot.
3. Grant su permission.
```
adb connect 192.168.1.129
adb -s 192.168.1.129 shell
eros-p1:/ $ su
```
4. Superuser request pop up on Magisk App and Grant
5. Go to settings (Gear Icon at top right corner in Magisk App) and `Enable Zygisk`.
6. In settings `Add Denylist` and add `Google Play Service`. To see `Google Play Service`, you need to check `OS Apps` and/or `System Apps`.
7. reboot
8. Install magisk safetynet fix module
```
adb -s 192.168.1.129 push safetynet-fix-v2.3.1.zip /data/local/tmp
adb -s 192.168.1.129 shell
eros-p1:/ $ su
eros-p1:/ # cd /data/local/tmp/
eros-p1:/ # magisk --install-module safetynet-fix-v2.3.1.zip
- Device is system-as-root
*************************
 Universal SafetyNet Fix
 by kdrag0n
*************************
*******************
 Powered by Magisk
*******************
- Extracting module files
- Done
```
8. Install pokemongo, Atlas and yasnac (Optional to check Sefetynet Pass check)
```
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 install pokemongo_0.249.2.apk
Success
user@linuxpc:~/Atlas$ adb install PokemodAtlas-Public-v22071801.apk
Success
user@linuxpc:~/Atlas$ adb install yasnac-v1.1.5.r65.15110ef310-release.apk
Success
9. Edit atlas_config.json and push
user@linuxpc:~/Atlas$ nano atlas_config.json (edit)
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 push atlas_config.json /data/local/tmp
atlas_config.json: 1 file pushed. 0.0 MB/s (182 bytes in 0.039s)
```
10. Add Denylist to pokemongo and yasnac, and then reboot
11. Check safetynet with yasnac to make sure basic integrity pass. `CTS profile match` will fail but OK. Magisk 25.2 may pass `CTS profile match`. 
12. Start Atlas. Superuser Request and Grant. Make sure name/endpoint are correct. And then Start.
13. Activate the device at atlas dashboard

Done!

## Reference
1. Magisk installation with fastboot steps are based on [https://github.com/topjohnwu/Magisk/blob/master/docs/install.md](https://github.com/topjohnwu/Magisk/blob/master/docs/install.md).
2. 
