# X96 mini / X96Q h313 chipset
This is steps for X96 mini h313 and X96Q h313 chipset 2g/16g ATV. These devices are Android 10, 32bit mode, wifi available as of 01/11/2023.

## Requirements

Before you start, you need:

- Linux PC (Windows may work but not tested and there is a report that windows failed fastboot boot command)
- Android Platform-tool for `adb`/`fastboot` and installed in your Linux PC.
  - Debian/Ubuntu-based Linux users can type the following command to install ADB:\
    ```sudo apt-get install android-tools-adb```
  - Fedora/SUSE-based Linux users can type the following command to install ADB:\
    ```sudo yum install android-tools```
- USB-A to USB-A cable for fastboot
- Keyboard and Mouse
- Magisk App apk.(I use [Magisk-v25.2.apk](https://github.com/topjohnwu/Magisk/releases/tag/v25.2))
- Magisk safetynet fix module. (I use [safetynet-fix-v2.3.1.zip](https://github.com/kdrag0n/safetynet-fix/releases/tag/v2.3.1))
- Optional. YASNAC (short for Yet Another SafetyNet Attestation Checker) to check SafetyNet bypass success or not. (I use [yasnac-v1.1.5.r65.15110ef310-release.apk](https://github.com/RikkaW/YASNAC/releases/tag/v1.1.5))
- All files are saved in `/home/user/Atlas` directory and all commands executed from this directory in this instruction.


---
## Steps
### Install Magisk App
1. Connect Kyeboard/Mouse and boot. Then connect to wifi (or ethernet)\
   When navigate UI with mouse:\
   `left click`: select\
   `right click`: cancel/go back
2. Configure static IP address in your router if possible
3. Install Magisk App (25.2) (Example device IP is 192.168.1.129)
```
user@linuxpc:~/Atlas$ adb connect 192.168.1.129
* daemon not running; starting now at tcp:5037
* daemon started successfully
connected to 192.168.1.129:5555

user@linuxpc:~/Atlas$ adb -s 192.168.1.129 install Magisk-v25.2.apk
Success

user@linuxpc:~/Atlas$ adb -s 192.168.1.129 reboot
```
### Extract boot.img and vbmeta.img
connect again after reboot
```
user@linuxpc:~/Atlas$ adb connect 192.168.1.129
connected to 192.168.1.129:5555
```
4. Extract boot.img in `/dev/block/by-name/` and copy to `/sdcard/Download/boot.img`
```
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 shell "echo 'dd if=/dev/block/by-name/boot of=/sdcard/Download/boot.img' | su"
65536+0 records in
65536+0 records out
33554432 bytes (32 M) copied, 0.749928 s, 43 M/s
```
5. Extract vbmeta.img in `/dev/block/by-name/` and copy to `/sdcard/Download/vbmeta.img`
```
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 shell "echo 'dd if=/dev/block/by-name/vbmeta of=/sdcard/Download/vbmeta.img' | su"
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
- Installing: 25.2 (25200)
- Copying image to cache
- Unpacking boot image
- Checking ramdisk status
- Stock boot image detected
- Patching ramdisk
- Repacking boot image

****************************
Output file is written to
/storage/emulated/0/Download/magisk_patched-25200_xxxxx.img
****************************
- All done!
```

5. Go to Linux terminal. Copy patched image `magisk_patched-25200_xxxxx.img` and `vbmeta.img` to Linux PC
```
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 pull /sdcard/Download/magisk_patched-25200_xxxxx.img
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 pull /sdcard/Download/vbmeta.img
```
6. Connect to ATV device. Copy original `su` from `/system/xbin/` to Linux pc for backup in case fastboot failed. Then, remove `su` binary from `/system/xbin/` before fastboot by
```
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 pull /system/xbin/su
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 shell
eros-p1:/ $ su
eros-p1:/ # mount -o remount,rw /
eros-p1:/ # rm /system/xbin/su
```
7. Connect USB to most left port and Linux USB and reboot with recovery. Most left port is USB-USB, right USB connect keyboard and no Mouse connected. You will need to use Keyboard to get to Fastboot Mode after reboot. 
In Linux Termianl
```
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 reboot recovery
```
8. Select `Enter fastboot` by Keyboard -> Select `Reboot to bootloader` by Keyboard. Then you will see Android with opening his berry.
9. Check device connected in Linux terminal
```
user@linuxpc:~/Atlas$ sudo fastboot devices
[sudo] password for user:
2c001474112607f1c4e     fastboot
```
10. Flash patched boot.img with command:
```
user@linuxpc:~/Atlas$ sudo fastboot flash boot magisk_patched-25200_xxxxx.img
target reported max download size of 33554432 bytes
sending 'boot' (32768 KB)...
OKAY [  1.505s]
writing 'boot'...
OKAY [  0.605s]
finished. total time: 2.110s
```
11. Flash vbmeta.img with command:
```
user@linuxpc:~/Atlas$ sudo fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
target reported max download size of 33554432 bytes
sending 'vbmeta' (16384 KB)...
OKAY [  0.759s]
writing 'vbmeta'...
OKAY [  0.331s]
finished. total time: 1.090s
```
12. Reboot with command:
```
user@linuxpc:~/Atlas$ sudo fastboot reboot
```
### Configure Magisk
1. Remove USB-USB cable and connect Mouse back
1. Open Magisk App and make sure Magisk 25.2 is installed.
2. If asked reboot, reboot.
3. Grant su permission.
```
user@linuxpc:~/Atlas$ adb connect 192.168.1.129
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 shell
eros-p1:/ $ su
```
4. Superuser request pop up on Magisk App and Grant
5. Go to settings (Gear Icon at top right corner in Magisk App) and `Enable Zygisk`.
6. In settings `Add Denylist` and add `Google Play Service`. To see `Google Play Service`, you need to check `OS Apps` and/or `System Apps`.
7. reboot
8. Install magisk safetynet fix module
```
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 push safetynet-fix-v2.3.1.zip /data/local/tmp
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 shell
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
user@linuxpc:~/Atlas$ adb -s 192.168.1.129 install pokemongo_0.263.1.armeabi-v7a.apk
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
11. Check safetynet with yasnac to make sure basic integrity pass. `CTS profile match` will fail but it's OK, as long as basic integrity pass, Pokemon Go will run.  
12. Start Atlas. Superuser Request and Grant. Make sure name/endpoint are correct. And then Start.
13. Activate the device at atlas dashboard

Done!

---
## Update (1/16/2024)
It requires to pass PlayIntegrity for BASIC and DEVICE now. You need to update play store and valid fingerprint to pass **DEVICE** integrity. 
1. Get play store from apkmirror.com. I use [Google Play Store (Android TV) 38.7.30 universal](https://www.apkmirror.com/apk/google-inc/google-play-store-android-tv/google-play-store-android-tv-38-7-30-release/).
2. Rename downloaded play store apk to Tubesky.apk
3. Copy to ATV and replace with current Tubesky.apk. Most likely system disk space was full and you need to remove some apks including current Tubesky.apk to free up some space for play store before copy from /data/local/tmp to /system/priv-app/Tubesky/. I deleted LiveTv.apk and DragonAgingTV.apk in addition to Tubesky.apk. Not sure exactry what these apks for but so far no issue for removing these apks.
   ```
   adb -s 192.168.1.129 push Tubesky.apk /data/local/tmp
   adb -s 192.168.1.129 shell
   su
   mount -o rw,remount /
   rm /system/priv-app/Tubesky/Tubesky.apk
   rm /system/priv-app/LiveTV/LiveTV.apk
   rm /system/app/DragonAgingTV/DragonAgingTV.apk
   cp Tubesky.apk /system/priv-app/Tubesky.apk
   ```
5. Download [PlayIntegrityFix.zip](https://github.com/chiteroman/PlayIntegrityFix/releases/tag/v15.1) or [PlayIntegrityFork-v5.zip](https://github.com/osm0sis/PlayIntegrityFork/releases/tag/v5) and install. Both modules won't provide valid fingerprint out of box as of 1/16 and you need to find valid fingerprint yourself.
   ```
   adb -s 192.168.1.129 push PlayIntegrityFix.zip /data/local/tmp
   adb -s 192.168.1.129 shell
   su
   magisk --install-module PlayIntegrityFix.zip
   ```
6. Reboot the ATV
7. Install Play Integrity Checker like Play Integrity API Checker or Simple Play Integrity Checker but these could be throtted due to too many API call.
8. Find valid fingerprint
    - Option 1: Use [Play Integrity Fix Props Collection](https://github.com/TheFreeman193/PIFS) to get a valid fingerprint from collection of fingerprints(These files aren't tested - They're just a sample of device profiles available online). You might be able to find a valid fingerprint if you are luckey. Even you could find good fingerprint, they won't last permanently since many people will use the script and use the same valid fingerprints. To use PIFS correctly, I needed to use older [commit](https://github.com/TheFreeman193/PIFS/tree/3bea229861257ded018769b48b64adcce0d4150e) to properly download profile/fingerprint collection from GitHub.
    ```
    adb -s 192.168.1.100 shell
    cd /data/local/tmp
    su
    /data/adb/magisk/busybox wget -O pickaprint.sh "https://raw.githubusercontent.com/TheFreeman193/PIFS/3bea229861257ded018769b48b64adcce0d4150e/pickaprint.sh"
    chmod 755 ./pickaprint.sh
    ./pickaprint.sh
    ```
    It will download json files and randomly select a fingerprint from collections and ask to check PlayIntegrity. Run PlayIntegrity checker to get result. If it passes both BASIC and DEVICE integrity type `y`, if it passes only BASIC, type `n` and keep going until it passes DEVICE integrity. If you are luckey, you will find a vlid one very quick. Good luck!
    - Option 2: Get cheap Android phone/tablet and extract fingerprint. [Here is some info how to extract fingerprint from device](https://github.com/chiteroman/PlayIntegrityFix/issues/116).



## Reference
1. Magisk installation with fastboot steps are based on [https://github.com/topjohnwu/Magisk/blob/master/docs/install.md](https://github.com/topjohnwu/Magisk/blob/master/docs/install.md).
