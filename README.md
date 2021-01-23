# Dell-unlock-undervolting
A guide to unlock voltage control on Dell laptops
#Disclaimer
If you follow the instructions correctly there is no chance of bricking your laptop. I will not take responsibilty if you brick your laptop. (Note: *You can clear CMOS by holding the power button for 30s on XPS laptops*)

#Requierments
* Universal IFR Extractor [Link](https://github.com/LongSoft/Universal-IFR-Extractor/releases/tag/v0.3.6)
* Dell PFS Extract [Link](https://github.com/platomav/BIOSUtilities/releases/tag/DPBE-v4.6)
* UEFITool [Link](https://github.com/LongSoft/UEFITool/releases)
* The latest BIOS installer for your laptop 
* modGrubShell EFI [Link](https://github.com/datasone/grub-mod-setup_var/releases/tag/1.1)
----

Step 1. Extracting the BIOS from the installer

* Copy the Dell BIOS update tool to the directory containing Dell PFS Extract 
![Step 1](link)
* Open CMD and cd to the directory containing Dell PFS Extract
![Step 2](link)
* Enter this into CMD "Dell_PFS_Extract.exe (Directory to the Dell BIOS update exe)"	(Note: if the directory contatins spaces add quotes around it (ex."C:/Dir/Thank you dell/")
![Step 3](link)
* A new folder will appear in the Dell PFD Extract directory containing your extracted BIOS

Step 2. Finding the Overclocking lock(CPU Voltage lock) Address 

* Open UEFITool click file, open new image file. Navigate to the new folder created in the Dell PFD Extract directory, select System BIOS with BIOS guard and click open.
![Step 2 pt 1](link)
* Hit CRTL+F and switch to text search. Type Overclocking Lock and hit enter. UEFITool should show "Unicode Text Overclocking Lock in setup/PE32..." Double click on this entry.
![Step 2 pt 2](link)
* Right click on the entry UEFITool brings you to. Click extract as is.
 
Step 3. Universal IFR Extractor

* Open Universal IFR Extractor and navigate to the file you extracted from the previous step open this, click extract.
![Step 3 pt 1](link)
* Open the text document that Universal IFR Extractor returned. Search for Overclocking Lock
![Step 3 pt 2](link)
* Copy down the variable mentioned beside "Setting: Overclocking Lock". In my case "0x789"

Step 4. Unlocking voltage control

* Format a USB to FAT32
![Step 3 pt 1](link)
* Make a folder called "EFI" and within that a folder called "BOOT"
![Step 3 pt 2](link)
* Rename modGRUBShell.efi to bootx64.efi and place it in "BOOT"
![Step 3 pt 3](link)
* Boot to this usb and enter "setup_var_3 "variable you got earlier" 0x00 (In my case "setup_var_3 0x789 0x00")

* Type "halt" and unplug the USB
