# tn20k-uart-peripherals

A small memory-mapped peripheral subsystem for the Tang Nano 20K (Gowin GW2AR-18C),
written in Verilog. Peripherals are accessed over UART through a register interface.

Built with the open source toolchain: Yosys, nextpnr, apycula, openFPGALoader.

## Status

Work in progress. See the roadmap below.

- [x] Toolchain setup and blink bring-up
- [ ] UART transmitter
- [ ] UART receiver
- [ ] Command parser FSM
- [ ] Register file
- [ ] PWM, timer, debounce peripherals
- [ ] Host CLI tool

## Hardware

- Board: Sipeed Tang Nano 20K
- FPGA: Gowin GW2AR-LV18QN88C8/I7
- System clock: 27 MHz (pin 4)

## Build

Requires the OSS CAD Suite on PATH.

```
yosys -p "read_verilog blink.v; synth_gowin -top top -json blink.json"
nextpnr-himbaechel --json blink.json --write blink_pnr.json --device GW2AR-LV18QN88C8/I7 --vopt cst=tangnano20k.cst --vopt family=GW2A-18C
gowin_pack -d GW2A-18C --compress -o blink.fs blink_pnr.json
openFPGALoader -b tangnano20k blink.fs
```

Loading with `-f` writes to flash instead of SRAM, so the design survives a power cycle.

## Notes

The GW2AR variant shares an id-code with the GW2A, so nextpnr and gowin_pack both
target `GW2A-18C` while the physical part is a GW2AR-18C. Gowin's own programmer
rejects the resulting bitstream over the id-code mismatch; openFPGALoader accepts it.

On Windows the JTAG interface needs the WinUSB driver (Zadig, interface 0). Leave
interface 1 alone, it carries the UART.
