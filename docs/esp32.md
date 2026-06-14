## Garmin + u-blox integration

### ESP32

The protocol has yet to be defined, but it is envisaged that the ESP32 will send data to APPro (or similar) once per second.

- Live data for use by APPro
  - Latitude + Longitude
  - Speed (SOG) + Course (COG) - 1 second averages
  - sAcc + hAcc - TBC
- UBX data
  - UBX payload definition - bit mask
  - UBX payload - byte array

The live data should use 1 second averages for SOG and COG, described in a [decimation](https://logiqx.github.io/gps-wizard/ideas/decimation/) article.

The [protocol](protocol.md) page describes the recommended items for the UBX payload.

Note: Various UBX data types have been reduced in size, but still remain suitable for speed sailing.