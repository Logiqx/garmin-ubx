## Garmin + u-blox integration

### ESP32

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send data to APPro (or similar) once per second.

- Live data for use by APPro
  - Latitude + Longitude - 1 second average
  - Speed (SOG) + Course (COG) - 1 second average
  - sAcc + hAcc - error propagation should relate to latitude + longitude + SOG
  - Number of satellites in use
- UBX data for logging to the FIT
  - UBX payload definition - simple bit mask
  - UBX payload - 120 byte array for one seconds worth of 5 Hz data

It might also be desirable to send things such as HR data from the Garmin to the ESP32.



#### Notes

The live data should use 1 second averages for SOG and COG, described in a [decimation](https://logiqx.github.io/gps-wizard/ideas/decimation/) article.

sAcc and hAcc should use applicable error propagation, so they relate to the 1 second averages.

The [protocol](protocol.md) page describes the recommended items for the UBX payload.

Various UBX data types have been reduced in size, but still remain suitable for speed sailing.

