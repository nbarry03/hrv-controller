# HRV Controller
>**Warning: This is an overview and is not intended to be an exhaustive guide. Use your own judgement and don't burn your house down.**

This project was created to control an HRV system with a [4 wire remote](https://my.daikinnortheast.com/product/control-ventilation-advanced-wall/01tRn000006Wke3IAC). Many smart thermostats can integrate with and control an HRV, but my thermostat only works with 2 wire remote systems, and therefore could not directly be connected.

An ESP8266 with a DAC was used to spoof the remote signals described below, triggering the HRV to run based on the combination of voltages. Because the ESP8266 has wifi and can be integrated into ESPHome, this allows me to use ESPHome to write firmware and control the state of the remote through Home Assistant, or in my case, controlled by AppDaemon and interacting with HASS via the AppDaemon HASS integration.

# The remote
The remote has 4 modes; econo, vent, 20 min/hr, and off. Econo runs the system with a low fan speed, while 20 min/hr runs at max speed but intermittently. Vent runs the system with the fan max speed. The control voltages for each mode are summarized below, with the wire color and remote terminal label denoted as follows: `Wire color (remote label)`.

| Mode | Red (R) | Blue (Bl) | Green (G) | Black (W) |
| --- | --- | --- | --- | --- |
| **Econo** | 5V | - | 3.4V | 3.1V |
| **Vent** | 5V | - | 3.4V | 3.1V |
| **20 min/hr** | 5 | - | 4.4V | 2.0V |
| **Off** | 5V | - | 1.4V | 2.0V |

> ***Note:** The black wire connected to the W terminal.

Based on the measured voltages there are 2 signal wires, green (G) and black (W), with voltage from red (R) and blue (Bl) as ground. Setting the voltages on the green (G) and black (W) signal wires results in the selection of a ventilation mode.

# BOM
| Part | Qty | Note |
| --- | --- | --- |
| ESP8266 | 1 | I used this because it was what I had on hand and worked nicely with the shields below. There may be a more modern alternative like a [Seeed Studio XIAO](https://www.google.com/search?q=seeed+xiao&oq=seeed+xiao&gs_lcrp=EgZjaHJvbWUyCQgAEEUYORiABDIKCAEQLhiABBjlBDIKCAIQLhiABBjlBDIHCAMQABiABDIHCAQQABiABDIHCAUQABiABDIHCAYQABiABDIHCAcQABiABDIHCAgQABiABDIHCAkQABiABNIBCDQxNzhqMGo3qAIIsAIB8QV_QfUlSrnyiw&sourceid=chrome&ie=UTF-8) |
| [Protoboard shield for Wemos D1-Mini](https://www.aliexpress.com/item/1005008599746081.html?spm=a2g0o.order_list.order_list_main.15.1d2518022HH3uu) | 1 | Soldered headers to attach some screw terminals (I believe for 5V and Gnd). This isn't required if you don't mind wires hanging off |
| [PCB screw terminal block](https://www.aliexpress.com/item/1005006363650525.html?spm=a2g0o.order_list.order_list_main.20.1d2518022HH3uu) | 1x 2 pin terminal | For 5V and Gnd screw terminals. The signal wires are not connected in the same way since the signal voltages come from a separate board. |
| STEMMA QT/Qwiic JST SH 4-pin cable (30mm) | 2 | Connects the I2C shield to the I2C level shifter and DAC |
| [TFT 0.85 shield (display)](https://www.aliexpress.com/item/1005006839059889.html?spm=a2g0o.order_list.order_list_main.35.1d2518022HH3uu) | 1 | (Optional) I originally thought a display might be useful, but it has been tricky to set up and I barely look at it. |
| [MCP4728 Quad DAC](https://www.aliexpress.com/item/1005008471916833.html?spm=a2g0o.order_list.order_list_main.50.1d2518022HH3uu) | 1 | Communicates via I2C and sets the signal voltages. |
| [STEMMA QT 3V to 5V level booster](https://www.digikey.ca/en/products/detail/adafruit-industries-llc/5649/21283813?s=N4IgTCBcDaIMoBUCiBZFBBABARQZgzAGqYIDymArMQDJKFLWYBCppiSASpiALoC%2BQA) | 1 | Boosts the 3.3V signal from the ESP8266 to a max of 5V. Required since the controller uses voltages up to 4.4V while the ESP8266 can only output 3.3V |
| M3 heatset insert | 4 | For attaching the DAC and level booster stack to the face plate |
| M1.7 3mm screws | 4 | I think this was the size I ended up using, but am not 100% sure... They need to be pretty small though. These attach the screen and ESP/shield stack to the faceplate. |
| [TFT I2C shield](https://www.aliexpress.com/i/32846977179.html) | 1 | For I2C communication to the level booster and DAC. |

> **Note:** Some ESP8266 models are clones of the Wemos D1 Mini and have the same IO pinout, which is what the shield boards are designed for. Not all ESP8266 dev boards are compatable and may have different pinouts. Be sure to double check that the pins match if you are using these shields.

The required 3D prints are provided in [stl](./stl/). You will need 1 face plate and 4 of the spacers for the DAC and level booster stack.

# Assembly
## ESP8266 stack
The ESP8266 stack was assembed using a combination of header pins for each board in the order below.

```
ESP8266
-------------------------
Protoboard (5V and Gnd screw terminals)
-------------------------
I2C shield
-------------------------
TFT 0.85 shield (display)
-------------------------
Faceplate
```

![assembly](./img/assembly.png)

