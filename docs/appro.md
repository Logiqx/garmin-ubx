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
  - SOG and COG from the Garmin API should still be written to the FIT.

- The UBX payload (and the definition) simply need to be written to the FIT as two separate fields.



### Nuances

Garmin watches write records to the FIT file once per second, but the timing of BLE events may not be suitable.

To ensure that all payloads are handled correctly (live results, and FIT contributor), [double-buffering](https://en.wikipedia.org/wiki/Multiple_buffering) is proposed.

- BLE event writes to buffer 1, whilst a timer event (every second) reads from buffer 2 (initially empty)

- BLE event writes to buffer 2, whilst a timer event (every second) reads from buffer 1
- ... etc

Hopefully this approach will ensure that all UBX payloads are safely written to the FIT file. TBC

