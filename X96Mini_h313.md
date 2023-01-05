x96 mini h313
Prerequirements
1. Linux PC
2. Android Platform-tool for adb/fastboot 
   Just use apt-get command to install
3. USB-A to USB-A cable for fastboot
4. Keyboard and Mouse


1. Connect Kyeboard/Mouse and boot. Then connect to wifi (or ethernet)
2. Configure static IP address in your router if possible
3. Install Magisk App (24.3) by
Example your device is 192.168.1.129

adb connect 192.168.1.129
* daemon not running; starting now at tcp:5037
* daemon started successfully
connected to 192.168.1.129:5555

adb install Magisk-v24.3.apk
Success

adb reboot

connect after reboot
adb connect 192.168.1.129
connected to 192.168.1.129:5555

extract boot.img by

adb shell "echo 'dd if=/dev/block/by-name/boot of=/sdcard/Download/boot.img' | su"
65536+0 records in
65536+0 records out
33554432 bytes (32 M) copied, 0.749928 s, 43 M/s

extract vbmeta.img by
adb shell "echo 'dd if=/dev/block/by-name/vbmeta of=/sdcard/Download/vbmeta.img' | su"
32768+0 records in
32768+0 records out
16777216 bytes (16 M) copied, 0.386947 s, 41 M/s

Patch boot.img by 
1. Open Magisk App
2. Install
3. Go to Download folder and select(double click) boot.img
4. Let's go
5. you will get like this

Pull patched boot.img to your PC (Linux) by
adb pull /sdcard/Download/magisk_patched-24300_xxxxx.img

Pull vbmeta by
adb pull /sdcard/Download/vbmeta.img

remove su binary from /system/xbin/ before fastboot by
adb shell
su
mount -o rw,remount /
rm /system/xbin/su

connect USB to most left port and Linux USB and reboot with recovery
exit adb shell
adb reboot recovery

go to Enter Fastboot by keyboard
Reboot to bootloader
You should get this 

sudo fastboot devices
[sudo] password for mizu:
2c001474112607f1c4e     fastboot

sudo fastboot flash boot /home/mizu/Atlas/magisk_patched-24300_egfZ4.img
target reported max download size of 33554432 bytes
sending 'boot' (32768 KB)...
OKAY [  1.505s]
writing 'boot'...
OKAY [  0.605s]
finished. total time: 2.110s

sudo fastboot --disable-verity --disable-verification flash vbmeta /home/mizu/Atlas/vbmeta.img
target reported max download size of 33554432 bytes
sending 'vbmeta' (16384 KB)...
OKAY [  0.759s]
writing 'vbmeta'...
OKAY [  0.331s]
finished. total time: 1.090s

sudo fastboot reboot

Open Magisk App
If asked reboot, reboot.
adb connect 192.168.1.129
adb shell
eros-p1:/ $ su

Superuser request pop up on Magisk App and Grant
Enable Zygisk
reboot

Open Magisk App
Add Denylist
Google Play Service

reboot

Install magisk safetynet fix module
adb push safetynet-fix-v2.3.1.zip /data/local/tmp

 magisk --install-module safetynet-fix-v2.3.1.zip
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

mizu@rdm:~/Atlas$ adb install pokemongo_0.249.2.apk
Success
mizu@rdm:~/Atlas$ adb install PokemodAtlas-Public-v22071801.apk
Success
mizu@rdm:~/Atlas$ adb install yasnac-v1.1.5.r65.15110ef310-release.apk
Success
mizu@rdm:~/Atlas$ nano atlas_config.json
mizu@rdm:~/Atlas$ mizu@rdm:~/Atlas$ adb push atlas_config.json /data/local/tmp
atlas_config.json: 1 file pushed. 0.0 MB/s (182 bytes in 0.039s)

Change Denylist config

reboot

Check safetynet with yasnac to make sure basic pass like this

Start Atlas
Superuser Request and Grant
Start
Activate at atlas dashboard
