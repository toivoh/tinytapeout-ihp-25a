<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

This is an example of the using the https://github.com/toivoh/pio-ram-emulator RAM emulator for Tiny Tapeout.
The RAM is used to store a frame buffer, 320x480 at 2 bits/pixel.
The frame buffer is continuously read to output a 640x480 @60 Hz VGA signal.
At the same time, the logic computes a Julia fractal, writing 16 bits to the frame buffer for every 8 pixels computed. After about a second, the whole frame buffer is filled in.

For more info about the RAM emulator, see https://github.com/toivoh/pio-ram-emulator/blob/main/docs/pio-ram-emulator.md.

The project contains some helper code for working with with the RAM emulator:

- `pio_ram_emulator.v` and `pio_ram_emulator.vh` (`sb_io.v` is also need) contain the modules `pio_ram_emu_transmitter` and `pio_ram_emu_receiver`
	- These are used to transmit and receive messages using the RAM emulator's message format
	- The design still has to follow the rules in https://github.com/toivoh/pio-ram-emulator/blob/main/docs/pio-ram-emulator.md about which messages can be sent when
	- See `julia_top.v` for an example of how to use these modules
- `test/pio_ram_emulator_model.v` contains a simulation model of the RAM emulator
	- See `test/tb.v` for an example of how to use the simulation model in a test
	- See `verilator/vtop.v` for an example of how to use the simulation model in a verilator setup
	- The model will try to detect behavior that violates the rules in https://github.com/toivoh/pio-ram-emulator/blob/main/docs/pio-ram-emulator.md, in which case it will set an error flag and stop responding (see the `ERROR_RESPONSE` parameter)
	- The simulation model is helpful, but might not capture the ful behavior of the RAM emulator. Please try to run your design on an FPGA against the actual RAM emulator as well.

## How to test

Plug in a [TinyVGA](https://github.com/mole99/tiny-vga) VGA Pmod to the output Pmod.
The https://github.com/toivoh/pio-ram-emulator RAM emulator must be running on the RP2040. **TODO: Instructions for how to set up.**
Start the project.

### Controls

The appearance of the Julia fractal is controlled by the `C` parameter, which can be seen as a complex value or 2d vector.
The `C` paramter can be changed using the `ui_in` port:

- `button_up` / `button_down` / `button_left` / `button_right` move the `C` value.
- `button_incstep` doubles the step length.
- `button_decstep` halves the step length.

A new `ui_in[5:0]` value must be stable for `2^19` cycles, or approximately 10 ms (at a 50.4 MHz clock rate), before it is accepted.
The `use_both_button_dirs` input changes how the input is interpreted:

- When `use_both_button_dirs = 0`, an input is triggered when one of the `button_` signals goes from high to low (and is stable for 10 ms). Recommended if the inputs are connected to buttons.
- When `use_both_button_dirs = 1`, an input is triggered when one of the `button_` signals goes from high to low or low to high (and is stable for 10 ms). Recommended if the inputs are connected to toggle switches.

## External hardware

This project needs a [TinyVGA](https://github.com/mole99/tiny-vga) VGA Pmod.
