## Garmin + u-blox integration

### APPro

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send data to APPro once per second.

- Live data for use by APPro
  - Latitude + Longitude - 1 second average
  - Speed (SOG) + Course (COG) - 1 second average
  - sAcc + hAcc - error propagation should relate to latitude + longitude + SOG
  - Number of satellites in use
- UBX data for logging to the FIT
  - UBX payload definition - simple bit mask
  - UBX payload - 120 byte array for one seconds worth of 5 Hz data



#### Notes

- Live data from the ESP32 should be used instead of values from the Garmin API.
  - SOG and COG from the Garmin API should still be written to the FIT.

- The UBX payload (and the definition) simply need to be written to the FIT as two separate fields.

- FIT records have a limit of 256 bytes, but the recommended UBX payload is is only 120 bytes.



#### Nuances

Garmin watches write records to the FIT file once per second, but the timings of BLE events may vary slightly.

To ensure that all payloads are handled correctly (e.g. live results, and FIT writer), [double-buffering](https://en.wikipedia.org/wiki/Multiple_buffering) is proposed.

- BLE event writes to buffer 1, whilst a timer event (every second) reads from buffer 2 (initially empty)

- BLE event writes to buffer 2, whilst a timer event (every second) reads from buffer 1
- ... etc

Hopefully this simple approach will ensure that all UBX payloads are safely written to the FIT file.

