# patchMMI
UCBAR's Rocket-chip generator supports a BlackBoxedROM in util/ROMGenerator.scala which contains the boot code.
The standard flow generates HEX files before synthesis and those contents cannot be changed after bitstream generation.
However, one may wish to use redundant BRAM resources in FPGA to host Firmware and may want to update it after bitstream is generated.

Xilinx Answer Record AR# 63041 cites two methods to be able to patch bitstreams with the contents of ELF files. 
patchMMI is an effort to generalize these methods for Xilinx Vivado.
XPM flow is recommended for patchMMI that converts Vivado-generated MMI file containing BRAM descriptions into Xilinx-specific Busblock and Buslane descriptions.

# Usage

1) Use XPM instance within BootROM. vlsi_rom_gen script is provided to replace one in $(rocketchip_dir)/scripts.
2) Generate MMI using following command withing Vivado after bitstream generation.

write_mem_info rom.mmi

3) Patch MMI as below.

patchMMI.py --i rom.mmi --o romMMI.mmi --d design.json

where design.json is the device tree in JSON format.

4) Finally, patch the bitstream design.bit with firmware binary firmware.elf as below.

updatemem -meminfo romMMI.mmi -data firmware.elf -proc rocketchip -bit design.bit -out designWP.bit
