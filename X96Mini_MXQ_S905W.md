# X96 Mini S905W / MXQ S905W Configuration
This is for X96 Mini / MXQ S905W Chip Configuration. ROM flashed in this instruction is Android 9, 32bit mode and wifi available.  
## Requirements
- Windows PC
- USB BURNING TOOL ([Amlogic_USB_Burning_Tool_v2.2.4](https://androidmtk.com/download-amlogic-usb-burning-tool))
- ROM to flash. I use from [atvXperience #884](https://forum.atvxperience.com/viewtopic.php?p=19913#p19913), [NON-SUPPORTED ROM](https://mega.nz/file/025z0KCY#D1b6S_kJn2H6fKvQbQr_epjlRJfnU0euu_0mqQemMAU) which has Magisk Root capability.
- Mouse and Keyboad
- USB-A to USB-A cable
- USB Memory to install Magisk
- Magisk safetynet fix module. (I use [safetynet-fix-v2.3.1.zip](https://github.com/kdrag0n/safetynet-fix/releases/tag/v2.3.1))
- Optional. YASNAC (short for Yet Another SafetyNet Attestation Checker). (I use [yasnac-v1.1.5.r65.15110ef310-release.apk](https://github.com/RikkaW/YASNAC/releases/tag/v1.1.5))
---
### Steps
1. Flash rom using USB BURNING TOOL (Amlogic_USB_Burning_Tool_v2.2.4)
   - X96 mini : **`Most left`** USB port (white USB port)
   - MXQ : **`Most right`** USB port
   1. Import ROM image downloaded.
   2. Click Start before connecting USB between PC to Device
   3. I recommand everything (Keyboard, mouse and power cable) is disconnected from device before start. Push button inside `AV` hole. You need very small screw driver to push the button. While pushing the button, connect USB to device. Then burning tool detects the device and starts flashing. You can remove screw driver once flashing started and progress bar is like 4%.
   4. Once successfully flashed. Push stop button and now you can disconnect USB.
2. Connect Keyboard, Mouse and Display then plugin power cable.
3. Connect wifi or ethernet and remember IP address.
4. Install Magisk with following steps (copy of HowTo_Magisk_Root.txt in MagiskRoot_setup folder).
> This version of ATVX has the capability to use Magisk Root via normal boot.
>  In order to replace the current ROOT with Magisk Root do the following:
>  1. Copy the TWRP.zip and Magisk file to a memory stick (pref FAT32 formatted)
>  2. Connect the memory stick to the device after the files were copied
>  3. Use the power menu to reboot to recovery
>  4. In the recovery menu, select the "Apply update from EXT" option
>  5. Select Update from udisk
>  6. Select the TWRP.zip file to boot into TWRP from the memory stick
>  7. Swipe to Allow Modifications
>  8. Select "Install"
>  9. Select "Select Storage"
>  10. Choose your Memory Stick (usually called USB OTG in the menu) and click OK
>  11. Select Magisk.zip
>  12. Swipe to Confirm Flash
>  13. Magisk will now remove old Root and install Magisk Root.
>  14. When the installation is done, select Reboot System to restart device
>  15. Enjoy Magisk Root :)
>
> DEVELOPER NOTES
> I included Magisk 19.3 as this one allows SafetyNet to pass successfully, eventhough it shows API error on this build when you do the test- it does actually pass. If you want to upgrade to the latest Magisk, and keep SafetyNet to pass, make sure that Magisk 19.3 is always first installed as per instructions abobe and then upgrade to the latest Magisk via the Magisk Manager. 
> 
> The latest Magisk Manager might complain about not being able to use or see version 19.x, this is fine, just continue and click on the "install" option next to Magisk. Select "Preserve AVB 2.0/dm-verity" and then next. Under Method select "Direct Install" and then "Let's Go" to start installing the latest version of Magisk. Select Reboot when done installing to finish the setup

5. Update Magisk to 22.1 in Magisk app. When first try to update to install, it will ask to allow install unknow apps and configure to allow always then select Magisk Manager to allow. Try again to update to install. After install, select Open and you will get warning about Unsupported Magisk Version, but just OK and continue.
6. Install 22.1. Select "Preserve AVB 2.0/dm-verity" and then next. Then select direct install. 
7. Reboot
8. Update Magisk App to 25.2 in Magisk app
9. Update Magisk to 25.2 in Magisk app with direct install. 
10. Reboot
11. Connect to device and Grant superuser access when pop up on display.
```
user@linuxpc:~/Atlas$ adb connect 192.168.1.201
connected to 192.168.1.201:5555
user@linuxpc:~/Atlas$ adb -s 192.168.1.201 shell
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

## Reference
- [Working ATVs for Atlas](https://github.com/madBeavis/PimpMyAtv/wiki/Working-ATVs-for-Atlas) by madBeavis
- [X96 Max Plus 5](https://docs.google.com/document/d/1SdaiJKAs2fJVMGLSI-TdORvwDh_THPAP8nqQbtEluno/edit#heading=h.oelb25v5f8ex) by BoomEX
- [MXQ S905W](https://docs.google.com/document/d/1PusB45JXq6WyolvezFZgAtQLZN_fPcvJ2FvAbAGihh0/edit) by geekygreek7
