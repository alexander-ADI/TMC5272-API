# TMC5272 Motor Control Driver

The TMC5272 driver included in this project provides a C API for ADI's Trinamic TMC5272, enabling: 

- IC initialization and application-specific configuration
- Motor movement via position & velocity control
- Trinamic Tricoder position feedback
- StallGuard2 configuration & sensorless stall detection

This driver establishes communications via the SPI bus. An example sketch of functionality is provided in `main.c`. 

## Hardware Requirements

- MAX32650FTHR evaluation board
- TMC5272-BOB (Break-Out Board)
- Stepper motor(s)

This API is compatible with most microcontrollers; however, the functions `tmc5272_SPI_readWrite` and `tmc5272_SPI_init` must be ported.

## Demo Setup Procedure

### 1. Hardware Setup

- Connect a TMC5272-BOB to the MAX32650 FTHR board via SPI1. (Use SS1, SS2, or SS3.) 
- Wire stepper motors to the TMC5272 A/B & C/D outputs.
    - Ensure that each ± A/B and ± C/D output pair is maintained.
    - Ensure that all motors match wiring polarity to ensure the same rotational direction.
- Connect TMC GND <-> MAX32650 GND.
- Provide 12V to the TMC5272 on VM. 
- Provide 3.3V to the TMC5272 on VDD. (This can be supplied via MAX32650FTHR.)

The example project outputs to 3 TMC5272-BOBs, on SS1.AB, SS2.AB & SS2.CD, and SS3.AB & SS3.CD.

> [!caution]
> Do not connect / disconnect a motor while power is enabled! This can cause current to flow into the TMC5272 drivers, potentially damaging the device.
>
> Additionally, turn on the 12V before providing 3.3V, and remove 3.3V before removing 12V. Otherwise, the board may draw power from the 3.3V rail. (I haven't had this break anything yet, but it certainly seems bad.)

### 2. Build & Flash

This project uses a .gitignore in order to only track application-specific files. Copy the repo's contents into a blank project from the MaximSDK `Examples` folder. 

Refer to [MSDK User Guide](https://analogdevicesinc.github.io/msdk//USERGUIDE/#getting-started-with-visual-studio-code) for documentation on building and flashing an application to the MAX32650FTHR.

### 3. Example Usage

The provided `main.c` demonstrates:

- Necessary setup steps:
    - Initializing SPI and TMC5272 devices using `tmc5272_dev_t`
    - Configuring motion profiles (velocity curves)
- Moving motors to absolute/relative positions
- Polling for arrival
- Basic Tricoder polling

See `TMC5272.h` for the full API.

### Application Notes

- Default register values are preset in `tmc5272_init()`. These can be left as-is, and should be good for most (NEMA17) stepper motors. (Use the Trinamic TMCL-IDE to explore alternative configurations.)

- Many API functions accept `ALL_MOTORS` to apply actions to both axes. Any functions that do not support this are noted in their function comments. Mostly these are `get()` functions for which it would not make sense to return a single value.

- Look at the `#define` statements in `main.c` and `TMC5272_SPI.h`; modify as needed.

### Acknowledgements & Licensing
This project is licensed under the MIT license, as detailed in this repo's LICENSE.txt.

This project uses and extends the [TMC-API](https://github.com/analogdevicesinc/TMC-API) provided by Analog Devices' Trinamic, which is licensed under the MIT license.