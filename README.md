## Supported Models

- XPS
    - 7590 Offset: 0x789
    - XPS 13 7390 (non 2 in 1), CFG Lock: 0x3E, Overclocking Lock: 0xDA
- Vostro
    - 5471 [#1](https://github.com/tadghh/Dell-unlock-undervolting/issues/1) Offset: 0x5D6 
- Latitude
    - 5491 [#3](https://github.com/tadghh/Dell-unlock-undervolting/issues/3) Offset: 0x659
    - 7300 [@delacor](https://github.com/delacor) (Tested on: BIOS 1.24) Offset: 0x789
- Precision 
    - 5520 [#4](https://github.com/tadghh/Dell-unlock-undervolting/issues/4) Offset: 0x59C 
- Optiplex
    - 3060 Micro [#5](https://github.com/tadghh/Dell-unlock-undervolting/issues/5) Offset: 0x65A

### ✔️ Compatibility
This guide should work with any laptop model that has a K series/unlocked CPU as an optional upgrade.
The following will work:
- H
- HK
- K

These versions are untested but may work:
- T
- P
- U

Doubtful any 12th gen or newer would work due to the CPU architecture changes.

### 📝 Note

If you are able to successfully unlock voltage control please make a pull request [here](https://github.com/tadghh/Dell-unlock-undervolting) adding your devices series/model to this list. 

# Dell/Lenovo Unlocking Voltage Control

This guide walks through the process of re-enabling voltage control on Dell/Lenovo laptops. Voltage control was 'removed' to prevent the plundervolt vulnerability (CVE-2019-11157). Even though Dell took way voltage control from the user, the exploit can still be taken advantage of because the variable controlling it was not made read only. This guide goes over rewriting the variable to restore the ability to undervolt/overvolt your CPU.

### ⚠️ WARNING

Modifying the incorrect EFI offset could brick your computer. Please do some research and make sure you have recovery methods.

# Required Tools 

There is an automated script below...

- Universal IRFE Extractor [Link](https://github.com/LongSoft/Universal-IFR-Extractor/releases/tag/v0.3.6)
    - VCRedist 2013 x86 [link](https://www.microsoft.com/en-us/download/details.aspx?id=40784)
- BIOS Utilities [Link](https://github.com/platomav/BIOSUtilities/tree/main)
    - Python 3.1X (10,11,12)
    - Make sure Python is included in $env:PATH
- UEFITool [Link](https://github.com/LongSoft/UEFITool/releases/latest)
- Your computers BIOS update utility (usually found on the laptops support page)
- modGrubShell EFI [Link](https://github.com/datasone/grub-mod-setup_var/releases/tag/1.1)
- USB with at least 1GB of storage

---

# Setup 

Download links are alongside the `required items` list. A PowerShell script has been included to setup everything.

### ❗ IMPORTANT

The below commands require PowerShell 7, otherwise you can manually download the files.

## ⚙️ Auto Setup

The following script will setup everything for you, just execute it in PowerShell

```powershell
mkdir C:\Users\$Env:UserName\Downloads\undervolting-tools;
Set-location C:\Users\$Env:UserName\Downloads\undervolting-tools;

## Download UEFI Tool, req: PS 7
[System.IO.Compression.ZipFile]::ExtractToDirectory([System.IO.MemoryStream]::new((Invoke-WebRequest -Uri "https://github.com/LongSoft/UEFITool/releases/download/A68/UEFITool_NE_A68_win32.zip").Content), $PWD);

## Modded grub shell
curl -LO https://github.com/datasone/grub-mod-setup_var/releases/download/1.1/modGRUBShell.efi;

## Bios utils
curl -L https://github.com/platomav/BIOSUtilities/archive/refs/heads/main.zip --output - | tar -xf - -C .;

## IRFE, req: PS 7
[System.IO.Compression.ZipFile]::ExtractToDirectory([System.IO.MemoryStream]::new((Invoke-WebRequest -Uri "https://github.com/LongSoft/Universal-IFR-Extractor/releases/download/v0.3.6/IRFExtractor_0.3.6_win.zip").Content), $PWD);

## Install deps for IRFE, req: winget
winget install -e --id Microsoft.VCRedist.2013.x86;
```

## 🐍 Python Setup

- Install Python 3.12 from the official [website](https://www.python.org/downloads/release/python-3124/)

## 🔧 Acquiring your laptops BIOS

- Usually located on the support page along with drivers
    - Look for category labeled BIOS or Firmware
    - Example https://www.dell.com/support/home/en-ca/product-support/product/xps-15-7590-laptop/drivers
- Most likely an executable file, rarely it could be in the format of an image/rom

# Step 1. Extracting the BIOS {#Step-1.-Extracting-the-BIOS }

1. Create a folder in your CWD called `bios_extract_dir`
    - Move the BIOS update/installer you downloaded into this folder
2. Using PowerShell cd into the location containing `BIOSUtilities`
    - Execute the following command
    - If the command fails `Error: This is not a supported input!` go to Step 1. Alt

```powershell
## using ../ as we are inside the folder and the extract dir should have been created in the parent folder
## it is expected that you have cd into the folder containing the bios utilities
## -i specifies the input folder
python .\dell_PFS_Extract.py -i ../bios_extract_dir
```

3. An `extract` folder will be created in the input (-i) folder
4. You will find a bin file in one of the folders, its filename will contain `System BIOS`

# Step 1. Alt Extracting the BIOS 

1. Open the installer, accept the TOS
2. Select "Extract Only"
    - If you don't have this, look online for a bios extraction tool specific to your model of laptop
3. Inside the extracted folder there should be a folder like `N40ET46W`
    - The folder name is specific to my installer, yours will be in a similar format
4. There will be a file that ends with `.FL1`, this is your bin file
    1. Make sure to change `UEFITool` to open any file type
    2. This option is at the bottom right in the open file dialog

# Step 2. Extracting PE32 Body 

1. Open `UEFITool` click file, open new image file. Navigate to the extract folder, open the `.bin` file mentioned previously.
2. At the top click Action -> Search.
    - Switch to the `Text` tab
3. In the text field paste `Overclocking Lock`
4. Double click one of the search results that mentions `Setup/PE32`
5. You should have a `PE32 image section` highlighted
    1. Right click on it and select `Extract as is`
    2. Save the file as is

# Step 3. Universal IRFE Extractor 

1. Open IRFE Extractor, click the `..`
    1. Navigate to the file you saved in the previous step
        - `Section_PE32_image_Setup.sct`
    2. Select it and click `Extract`
    3. Save the text file as is
        - In the extract folder beside all the bin files
        - By default the saved files name will be `Section_PE32_image_Setup IFR.txt`
2. Use a text editor (sublime, notepad++) and open `Section_PE32_image_Setup IFR.txt`
3. Using the text editors search function look for `Overclocking Lock`
4. Beside the result there should be text similar to below
    - `Overclocking ... VarStoreInfo (VarOffset/VarName): 0x789`
5. Write down the exact numbers/letters of the `offset`

    - In my case `0x789`

# Step 4. Unlocking Voltage Control

1. Format a USB with a `FAT32` partition
2. At the root of the USB create a `EFI` folder
    - Create a 'BOOT' folder inside of `EFI`
3. Rename `modGRUBShell.efi` to `bootx64.efi`
    - place it inside of `BOOT`
4. Set the USB as a boot device
    - Once you have booted off the USB you should see the GRUB command line
    - If your laptops screen is high resolution the text may be small

### ⚠️ WARNING

If you enter the incorrect offset you could brick your system. Double check!

5. Enter the following command
    - "setup_var_3 '`offset` you got in Step 3.5' 0x00"
    - For me the command looked like

```sh
setup_var_3 0x789 0x00
```

6. Once the command completes, check the result code
    - If it ends in 0x0 than the process worked
    - A result that ends in 0x8 means that write protection is enabled
        - There are ways to get around this, like leveraging [CVEs](https://support.lenovo.com/au/en/product_security/LEN-106014) ex `CVE-2023-2290` that allow arbitrary writes
            - The offset of LenovoFlashDeviceInterface can be found in the text file we made
        - Here is another guide going over [this](https://github.com/Cr4sh/fwexpl)
7. Type "halt", unplug the USB and reboot your computer
