## Garmin + u-blox integration

### APPro

The protocol has yet to be defined, but it is envisaged that the ESP32 will send data to APPro once per second.

- Live data for use by APPro
  - Latitude + Longitude
  - Speed (SOG) + Course (COG) - 1 second averages
  - sAcc + hAcc - TBC
- UBX data
  - UBX payload definition - bit mask
  - UBX payload - byte array

The role of APPro:

- Live data from the ESP32 should be used instead of values from the Garmin API.
- The UBX payload (and the definition) simply need to be written to the FIT as two separate fields.



### Nuances

Records will written to the FIT file exactly once per second, but the timing of BLE events may not be convenient.

To help to ensure that all payloads are used correctly (within live results, and written to the FIT), propose the use of [double-buffering](https://en.wikipedia.org/wiki/Multiple_buffering).

- BLE events write to the buffer which is not currently scheduled for writing.
- Timed events send the buffer which is scheduled for writing to the FIT.