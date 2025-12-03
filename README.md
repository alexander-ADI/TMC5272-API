# TMC5272 Motor Control Driver

This driver for TMC5272 provides a C API for ADI's Trinamic TMC5272, enabling: 

- IC initialization and application-specific configuration
- Motor movement via position & velocity control
- Trinamic Tricoder position feedback
- StallGuard2 configuration & sensorless stall detection

![A TMC5272-EVAL board with motor attached is controlled by a MAX32650FTHR board.](assets/demo.png)

The driver establishes communications via the SPI bus. UART is currently **not** supported.

## Driver Demo

The `main.c` file demonstrates implemented features, using a MAX32650FTHR as the host microcontroller.

This API is compatible with most microcontrollers; however, the functions `tmc5272_SPI_readWrite` and `tmc5272_SPI_init` must be ported.

### Hardware / Software Requirements

The demo uses the following hardware, according to the block diagram below.

- [MAX32650FTHR](https://www.analog.com/en/resources/evaluation-hardware-and-software/evaluation-boards-kits/max32650fthr.html)
- [TMC5272-BOB](https://www.analog.com/en/resources/evaluation-hardware-and-software/evaluation-boards-kits/tmc5272-bob.html) or [TMC5272-EVAL](https://www.analog.com/en/resources/evaluation-hardware-and-software/evaluation-boards-kits/tmc5272-eval.html)
    - (Either board can be used.)
- Stepper motors

![](assets/demo_block_diagram.png)

To run the demo using the above hardware, install the [Maxim SDK](https://github.com/analogdevicesinc/msdk). 

### Hardware Setup

> [!caution]
> Do not connect / disconnect a motor while power is enabled! This can cause current to flow into the TMC5272 drivers, potentially damaging the device.
>
> Additionally, turn on the 12V before providing 3.3V, and remove 3.3V before removing 12V. Otherwise, the board may draw power from the 3.3V rail. (I haven't had this break anything yet, but it certainly seems bad.)

Connect hardware in the following order:

1. Connect TMC5272-BOBs to the MAX32650 FTHR board via SPI1. (Use SS1, SS2, and SS3.) 
2. Wire stepper motors to the TMC5272-BOB A/B or C/D outputs.
    - Ensure that each ± A/B and ± C/D output pair is maintained.
    - Ensure that all motors match wiring polarity to ensure the same rotational direction.
3. Make sure shared ground is established (TMC GND <-> MAX32650 GND).
4. Provide power rails.
    1. Connect 12V to the TMC5272 on VM. 
    2. Connect 3.3V to the TMC5272 on VDD. (This can be supplied via MAX32650FTHR.)

The final TMC5272-BOB wiring should match the table below.

| Pin | Header Name | Connection |   | Pin | Header Name | Connection       |
|-----|-------------|------------|---|-----|-------------|------------------|
| 1   | VCC_IO      | 3.3V       |   | 15  | +VM         | 12V              |
| 2   | GND         | 0V         |   | 16  | GND         | 0V               |
| 3   | ENCA1       | -          |   | 17  | OD1         | M1.D1            |
| 4   | ENCB1/REFL1 | -          |   | 18  | OD2         | M1.D2            |
| 5   | ENCN1/REFR1 | -          |   | 19  | OC1         | M1.C1            |
| 6   | SPI_CSn     | SPI_CS     |   | 20  | OC2         | M1.C2            |
| 7   | SPI_SCK     | SPI_SCK    |   | 21  | GND         | 0V               |
| 8   | SPI_MOSI    | SPI_MOSI   |   | 22  | OA1         | M0.A1            |
| 9   | SPI_MISO    | SPI_MISO   |   | 23  | OA2         | M0.A2            |
| 10  | CLK         | 0V         |   | 24  | OB1         | M0.B1            |
| 11  | SLEEPn      | -          |   | 25  | OB2         | M0.B2            |
| 12  | ENCA2       | -          |   | 26  | GND         | 0V               |
| 13  | ENCB2/REFL2 | -          |   | 27  | DIAG0       | GPIO (Interrupt) |
| 14  | ENCN2/REFR2 | -          |   | 28  | DIAG1       | GPIO (Interrupt) |


### Software Setup

This project only tracks application-specific files. Copy the repo's contents into a blank project from the MaximSDK `Examples` folder. 

- Please refer to the [MSDK User Guide](https://analogdevicesinc.github.io/msdk//USERGUIDE/#getting-started-with-visual-studio-code) for instructions on building, flashing, and running projects.

### Application Notes

- Default register values are preset in `tmc5272_init()`. These can be left as-is, and should be good for most NEMA 17 stepper motors. (Use the Trinamic TMCL-IDE to explore alternative configurations.)

- Many API functions accept `ALL_MOTORS` to apply actions to both axes. Any functions that do not support this are noted in their function comments. Mostly these are `get()` functions for which it would not make sense to return a single value.


### Acknowledgements & Licensing
This project is licensed under the MIT license, as detailed in this repo's LICENSE.txt.

This project uses and extends the [TMC-API](https://github.com/analogdevicesinc/TMC-API) provided by Analog Devices' Trinamic, which is licensed under the MIT license.