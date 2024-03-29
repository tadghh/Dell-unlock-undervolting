# Supported Models

- XPS
  - 7590

- Vostro 
  - 5471 (https://github.com/Lyceris-chan)

Most likely any model that has a K series skew CPU

*If you are able to make it work on a model not already listed please create a issue with your devices model*

# Dell unlock Undervolting
A guide to unlock voltage control on Dell laptops above BIOS version 1.6.1,
Undervoltiong was locked away to prevent the Intel plundervolt vulnerability (CVE-2019-11157)

# Disclaimer
If you follow the instructions correctly there is little chance of bricking your laptop. I will not take responsibility if you brick your laptop. (Note: *You can clear CMOS by holding the power button for 30s on XPS laptops*)

# Requirements
* Universal IRFE Extractor [Link](https://github.com/LongSoft/Universal-IFR-Extractor/releases)
* Dell PFS Extract [Link](https://github.com/platomav/BIOSUtilities/releases)
* UEFITool [Link](https://github.com/LongSoft/UEFITool/releases)
* The latest BIOS installer for your laptop 
* modGrubShell EFI [Link](https://github.com/datasone/grub-mod-setup_var/releases/tag/1.1)
----

Step 1. Extracting the BIOS from the installer

* Copy the Dell BIOS update tool to the directory containing Dell PFS Extract 
![Step 1](https://github.com/tadghh/Dell-unlock-undervolting/blob/main/Pictures/step1pt1.PNG)
* Open CMD and cd to the directory containing Dell PFS Extract
* Enter this into CMD "Dell_PFS_Extract.exe (Directory to the Dell BIOS update exe)"	(Note: if the directory contains spaces add quotes around it (ex."C:/Dir/Thank you dell/")
![Step 2](https://github.com/tadghh/Dell-unlock-undervolting/blob/main/Pictures/step1pt2.PNG)
* A new folder will appear in the Dell PFD Extract directory containing your extracted BIOS

Step 2. Finding the Overclocking lock (CPU Voltage lock) Address 

* Open UEFITool click file, open new image file. Navigate to the new folder created in the Dell PFD Extract directory, select System BIOS with BIOS guard and click open.
![Step 2 pt 1](https://github.com/tadghh/Dell-unlock-undervolting/blob/main/Pictures/step2pt1.PNG)
* Hit CRTL+F and switch to text search. Type Overclocking Lock and hit enter. UEFITool should show "Unicode Text Overclocking Lock in setup/PE32..." Double click on this entry.
![Step 2 pt 2](https://github.com/tadghh/Dell-unlock-undervolting/blob/main/Pictures/step2pt2.PNG)
* Right click on the entry UEFITool brings you to. Click extract as is.
 ![Step 2 pt 3](https://github.com/tadghh/Dell-unlock-undervolting/blob/main/Pictures/step2pt3.PNG)
Step 3. Universal IRFE Extractor

* Open Universal IRFE Extractor and navigate to the file you extracted from the previous step open this, click extract.
* Open the text document that Universal IFR Extractor returned. Search for Overclocking Lock
* Copy down the variable mentioned beside "Overclocking Lock". In my case "0x789"
![Step 3 pt 1](https://github.com/tadghh/Dell-unlock-undervolting/blob/main/Pictures/step3pt1.PNG)
Step 4. Unlocking voltage control

* Format a USB to FAT32
* Make a folder called "EFI" and within that a folder called "BOOT"
* Rename modGRUBShell.efi to bootx64.efi and place it in "BOOT"
* Boot to this usb and enter "setup_var_3 "variable you got earlier" 0x00 (In my case "setup_var_3 0x789 0x00")
* Some laptops require manaul boot device entry to appear in the boot list
* Type "halt" and unplug the USB and reboot your computer
