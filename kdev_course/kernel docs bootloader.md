# bootloader
this is the first software that runs after bios/uefi has loaded the hardware. 

# boot process

### power-on and firmware execution
- when it it turned on, then cpu starts executing firmware instructions stored in the ROM
- the firmware can be bios(legacy boot) or uefi (modern boot):
	- bios is for mbr partitioning. and looks for a boot sector
	- uefi is for guid partitioning. and looks for efi partition to find bootloaders.



### bootloader execution
