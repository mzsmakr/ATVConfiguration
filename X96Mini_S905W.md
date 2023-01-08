# X96 Mini S905W / MXQ S905W Configuration
This is for X96 Mini / MXQ S905W Chip Configuration 
## Requirements

---
### Steps
1. Flash rom using USB BURNING TOOL (Amlogic_USB_Burning_Tool_v2.2.4)
2. Install Magisk with following steps.
3. Update Magisk to 22.1 in Magisk app. After install, select Open and you will get warning but just continue.
5. Install 22.1 with direct install. 
6. Reboot
7. Update Magisk App to 25.2 in Magisk app
8. Update Magisk to 25.2 in Magisk app with direct install. 
9. Reboot
10. Connect to device and Grant superuser access when pop up on display.
```
mizu@rdm:~/Atlas$ adb connect 192.168.1.201
connected to 192.168.1.201:5555
mizu@rdm:~/Atlas$ adb -s 192.168.1.201 shell
atvX:/ $ su (after this superuser request should pop up)
atvX:/ #
```
11. Enable Zygisk and Configure DenyList and add Google Play Service ( need to Show system apps)
12. reboot
13. Install magisk safetynet-fix module
```
user@linuxpc:~/Atlas$ adb connect 192.168.1.201
connected to 192.168.1.201:5555
user@linuxpc:~/Atlas$ adb -s 192.168.1.201 push safetynet-fix-v2.3.1.zip /data/local/tmp
safetynet-fix-v2.3.1.zip: 1 file pushed. 0.2 MB/s (95201 bytes in 0.586s)
user@linuxpc:~/Atlas$ adb -s 192.168.1.201 shell
atvX:/ $ su
atvX:/ # cd /data/lo
local/       log/         lost+found/
atvX:/ # cd /data/local/tmp/
atvX:/data/local/tmp # magisk --install-module safetynet-fix-v2.3.1.zip
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
atvX:/data/local/tmp #
```
15. Install pokemongo, Atlas, yasnac(optional) app and push atlas_config.json.
```
user@linuxpc:~/Atlas$ adb -s 192.168.1.201 install pokemongo_0.249.2.apk
Success
user@linuxpc:~/Atlas$ adb -s 192.168.1.201 install PokemodAtlas-Public-v22071801.apk
Success
user@linuxpc:~/Atlas$ adb -s 192.168.1.201 install yasnac-v1.1.5.r65.15110ef310-release.apk
Success
user@linuxpc:~/Atlas$ nano atlas_config.json
user@linuxpc:~/Atlas$ adb -s 192.168.1.201 push atlas_config.json /data/local/tmp
atlas_config.json: 1 file pushed. 0.0 MB/s (182 bytes in 0.014s)
user@linuxpc:~/Atlas$
```
16. Configure Denylist and add pokemongo and yasnac.
17. reboot
18. Open yasnac and check if safetynet pass or not. It should show Basic/CTS profile match both Pass.
19. Start Atlas and check name/endpoint and start.

Done!
