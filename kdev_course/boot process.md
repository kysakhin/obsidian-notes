- bios loads and checks for all hardware functioning
- then bios looks for a bootloader to boot by searching all the storage mediums for the boot signature "0X55AA"
- ![](https://i.sstatic.net/sSzt8.gif)
- this is just an example for mbr. most modern systems are uefi based. 
- the boot signature resides in the boot sector. which is usually at the 511th and 512th bytes of the first sector. 
- and while ssds dont use physical sectors in their physical build, they still do have logical sectors which are software defined and use a mapping table to translate logical sectors into physical locations.
- after it identifies the boot signature, then it loads it into address 0X7C00 which is standard location for loading the bootloader
- examples for bootloader in linux are GRUB. LILO, EFI boot loader. 
- the bootloader loads the operating system kernel into memory
- so the options to choose what os to load, after bot is presented by this grub.
- once the bootloader loads the kernel, full control is taken over by the kernel. 

### kernel init
1. kernel init: intializes its structures and data
2. hardware detection
3. loads device drivers
4. filesystem mounting. like the root
5. init system (systemd)
6. service startup which is handled by systemd