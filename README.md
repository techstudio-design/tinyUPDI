### What is *tinyUPDI*?

The ***tinyUPDI*** is a tiny programmer uses for programming *any* AVR that supports SerialUPDI interface. Unlike other UPDI programmers, it only requires one USB port and automatically switches between UPDI programming and debugging serial mode.

![tinyUPDI](https://github.com/techstudio-design/tinyUPDI/blob/main//tinyUPDI.png)

### Why did you make it?

Many modern AVR developers in the past, we included, hacks together a USB Serial adaptor with a few components 
together 
for programming UPDI-capable AVR chips. Those hacks work as a UPDI programmer, it is however in reality often 
requires another USB/UART 
ports for serial debugging printout.  The *tinyUPDI* 
supports automatic switch 
between UPDI and UART interfaces via a single USB port, saving the needs for connecting two USB ports.

### What makes it special?

- Single USB port connection supports both UPDI and debugging Serial Monitor
- Supports all UPDI-programmable AVRs
- Supports Arduino IDE and PlatformIo development envrionment
- Works with multiple Arduino Cores such as [megaTinyCore](https://github.com/SpenceKonde/megaTinyCore), [DxCore](https://github.com/SpenceKonde/DxCore) and should  [MegaCoreX](https://github.com/MCUdude/MegaCoreX)
- Based on latest [CP2101N](https://www.silabs.com/documents/public/data-sheets/CP2101.pdf) from Silicon lab.
- No USB driver required for macOS, Windows and Linux (kernel v2.6.12 onward)
- Capable of powering target board (5v only) directly via the 6-pin programming header from USB power source

### Working with megaTinyCore/DxCore

#### Arduino IDE

Instead of using the SerialUPDI implementation `pymcuprog.py` from Microchip, megaTinyCore/DxCore ships with its own version of SerialUPDI, When working with Arduino IDE, any of the SerialUPDI options in *Tools -> Programmers* would *work* with *tinyUPDI*. However, when using modes higher than 230400bps, it occasionally failed or in the case of running at  921600bps, the underneath programming function failed to read the data correctly and will fallback to 115200bps. We recommend to use the options for baudrate of 230400bps for the best performance between speed and reliablity.

### - Arduino IDE with Apple Silicon

The tinyUPDI works well under the Windows, Linux and MacOS in general. However, when using it with MacOS Apple Silicon machines, it does not output data to the Serial Monitor of Arduino IDE for some unknown reason. One workaround is to use an external terminal software such as [CoolTerm]([CoolTerm - Download](https://coolterm.en.lo4d.com/windows) where DTR state can be set, this works but is not seemless as you would need to disconnect the terminal before the programming.

Another altertive is to use PlatformIO as your IDE environment which provide better user experience between progamming mode and serial debugging mode. 

#### PlatformIO

*tinyUPDI* works well under PlatformIO with megaTinyCore with proper configuration. 

#### - Install pymcuprog

Intead of using the build-in programmer that is tightly integreated with megaTinyCore and works well with Arduino IDE, but not with PlatformIO, we recommend to download and install the [pymcuprog · PyPI](https://pypi.org/project/pymcuprog/) from Microchip.

#### - Configure PlatformIO.ini

Here is an example of `platformio.ini` configuration for running an Attiny3227 at 20MHz on a MacOS:

```
[env:ATtiny3227]
board = ATtiny3227
platform = atmelmegaavr
framework = arduino
board_build.f_cpu = 20000000L
monitor_speed = 115200
monitor_rts = 0

upload_protocol = custom
upload_port = /dev/cu.usbserial* ; for MacOS
upload_speed = 230400

; for using pymcuprog, download and install it via PyPi
upload_flags =
    --tool
    uart
    --device
    $BOARD
    --uart
    $UPLOAD_PORT
    --clk
    $UPLOAD_SPEED
upload_command = pymcuprog write --erase $UPLOAD_FLAGS --filename $SOURCE
```

The `monitor_rts = 0` flag is only required when operatiing under MacOS with Apple Silicon, it ensures that terminal would works upon opening correctly.

### Working with MegaCoreX

Unfortunately at this moment, we don't have any MCU to the tinyUPDI but we think it should work. We will provide update of this section once we have the chance to test it.
