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

The [protocol](protocol.md) page describes the items that can be included in the UBX payload.

Note that some of the UBX data types have been reduced in size, specifically for the speed sailing use cases.