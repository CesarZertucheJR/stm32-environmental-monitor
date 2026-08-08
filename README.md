# STM32 Environmental Monitor

An embedded environmental monitoring system built on the STM32F401RE (Nucleo board) that reads live temperature, humidity, and light data and displays it in real time on a character LCD, with data also streamed over UART.

## Overview

This project reads a DHT11 temperature/humidity sensor and a photoresistor (LDR) for ambient light, then displays the readings on a 16x2 character LCD (4-bit parallel interface) while streaming the same data over UART for logging or PC-side monitoring. An onboard push-button (external interrupt on PC13) cycles the LCD through three display screens: live readings, session min/max tracking, and a summary status message ("All Good!", "Too Hot!", "Too Humid!").

## Hardware

- **MCU:** STM32F401RET6 (Nucleo-F401RE), LQFP64
- **Display:** 16x2 character LCD, 4-bit parallel mode
- **Sensors:**
  - DHT11 \u2014 temperature & humidity (single-wire digital protocol)
  - Photoresistor (LDR) \u2014 ambient light (analog, via ADC)
- **Input:** Push-button on PC13 (external interrupt, falling edge) \u2014 cycles display screens

## Pinout

| Pin | Function |
|-----|----------|
| PA0 | Photoresistor / light sensor (ADC1_IN0) |
| PA1 | DHT11 data (bit-banged single-wire) |
| PA2 | USART2 TX |
| PA3 | USART2 RX |
| PA8 | LCD Enable (E) |
| PA9 | LCD Register Select (RS) |
| PA10 | LCD Data 7 (D7) |
| PB3 | LCD Data 6 (D6) |
| PB4 | LCD Data 5 (D5) |
| PB5 | LCD Data 4 (D4) |
| PC13 | User button (EXTI, falling edge) \u2014 cycles display screens |

## Firmware

- Built with STM32CubeMX / STM32CubeIDE (MDK-ARM toolchain)
- **DHT11 driver**: bit-banged single-wire protocol with microsecond-precision timing via TIM2, including start signal, response detection, 40-bit read, and checksum verification
- **ADC1**: single-channel conversion for the light sensor (12-bit resolution, converted to a 0\u2013100% scale)
- **LCD driver**: custom 4-bit parallel HD44780-style driver (init sequence, command/data writes, cursor positioning)
- **Display state machine**: three screens (Main, Min/Max, Summary) cycled via the PC13 button interrupt
- **Min/max tracking**: running session min and max for both temperature and humidity
- **USART2**: streams live sensor readings at 115200 baud for external logging

## Building & Flashing

1. Open `envmonitor.ioc` in STM32CubeIDE (or regenerate code via STM32CubeMX), which will scaffold the peripheral init code.
2. Copy `main.c` into `Core/Src/`.
3. Build the project for the STM32F401RETx target.
4. Flash to a Nucleo-F401RE board via ST-Link.
5. Wire the DHT11 to PA1, the photoresistor (with pull-down/voltage divider) to PA0, and the LCD per the pinout table above.
6. Open a serial terminal (115200 baud, 8N1) to view live readings over USART2.



## Author

Cesar Zertuche \u2014 [github.com/CesarZertucheJR](https://github.com/CesarZertucheJR)
