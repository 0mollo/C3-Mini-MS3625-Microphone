# Carenuity C3-Mini-MS3625-Microphone

![image alt](https://github.com/0mollo/C3-Mini-MS3625-Microphone/blob/main/MS3625%20Top%20View.png) ![image alt](https://github.com/0mollo/C3-Mini-MS3625-Microphone/blob/main/MS3625%20Bottom%20View.png)

Omnidirectional Digital Voice Capture Module  
Designed for the Carenuity Standardized Hardware Ecosystem (SMA)

## Overview

The Carenuity MS3625 Microphone Piggyback is a compact, digital omnidirectional microphone module designed specifically for integration with the ESP32-C3 Mini development board.

It exposes the I2S interface of the MS3625 MEMS microphone and follows the Carenuity standardized scalable hardware foundation for rapid prototyping and embedded product development.

Version: V1.0  
Form Factor: C3-Mini Ecosystem Compatible

## Key Features

- Digital MEMS Microphone (MS3625)
- Omnidirectional voice capture
- I2S Interface (SD, SCK, WS)
- 3.3V operation
- Direct header alignment with ESP32-C3 Mini
- Compact piggyback architecture
- Carenuity SMA compliant

## Pin Mapping (C3-Mini Integration)

### Microphone Pins

| MS3625 Pin | Function |
|------------|----------|
| VDD        | 3V3 |
| GND        | Ground |
| SD         | Serial Data |
| SCK        | Bit Clock |
| WS         | Word Select |

### Suggested ESP32-C3 Mini Mapping

| Microphone | C3 Mini GPIO |
|------------|--------------|
| SD         | GPIO4 |
| SCK        | GPIO6 |
| WS         | GPIO5 |
| VDD        | 3V3 |
| GND        | GND |

(GPIO mapping can be changed in firmware.)

## Schematics

[See](https://github.com/0mollo/C3-Mini-MS3625-Microphone/blob/main/MS3625%20Microphone.pdf)

## I2S Wiring Diagram

SD  → Data In  
SCK → Bit Clock  
WS  → LRCLK

## Example Arduino Code (ESP32-C3)

```cpp
#include <driver/i2s.h>

#define I2S_WS  5
#define I2S_SD  4
#define I2S_SCK 6

void setup() {
  Serial.begin(115200);

  i2s_config_t i2s_config = {
    .mode = I2S_MODE_MASTER | I2S_MODE_RX,
    .sample_rate = 16000,
    .bits_per_sample = I2S_BITS_PER_SAMPLE_32BIT,
    .channel_format = I2S_CHANNEL_FMT_ONLY_LEFT,
    .communication_format = I2S_COMM_FORMAT_STAND_I2S,
    .intr_alloc_flags = 0,
    .dma_buf_count = 4,
    .dma_buf_len = 256,
    .use_apll = false
  };
i2s_pin_config_t pin_config = {
    .bck_io_num = I2S_SCK,
    .ws_io_num = I2S_WS,
    .data_out_num = I2S_PIN_NO_CHANGE,
    .data_in_num = I2S_SD
  };

  i2s_driver_install(I2S_NUM_0, &i2s_config, 0, NULL);
  i2s_set_pin(I2S_NUM_0, &pin_config);
}
void loop() {
  int32_t sample;
  size_t bytes_read;

  i2s_read(I2S_NUM_0, &sample, sizeof(sample), &bytes_read, portMAX_DELAY);
  Serial.println(sample);
}
