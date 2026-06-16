## Garmin + u-blox integration

### ESP32

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send summary data to APPro (or similar) once per second.

- Latitude + Longitude - 1 second average
- Speed (SOG) + Course (COG) - 1 second average
- sAcc + hAcc - error propagation should relate to latitude + longitude + SOG
- Number of satellites in use

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send UBX payloads either 5 or 10 times per second.

- UBX payload - 20 byte array, suitable for the hardcoded Garmin [MTU](https://github.com/Logiqx/garmin-ubx/discussions/2) size

See the [protocol](protocol.md) page and [Google Sheet](https://docs.google.com/spreadsheets/d/1XAQbnhaoFV_E_tIXIC6ekw-PZZEP4lom5GkxAbc-hIk/edit?usp=sharing) for more details about the UBX payloads.

It might also be desirable to send things such as HR data from the Garmin to the ESP32.



#### Notes

The live data should use 1 second averages for SOG and COG, described in a [decimation](https://logiqx.github.io/gps-wizard/ideas/decimation/) article.

sAcc and hAcc should use applicable error propagation, so they relate to the 1 second averages.

The [protocol](protocol.md) page describes the recommended items for the UBX payload.

Various UBX data types have been reduced in size, but still remain suitable for speed sailing.



#### Higher Frequencies

10 Hz data would require a 200 byte array within each FIT record, which has a total limit of 256 bytes.

This should be ok, since existing FIT records do not occupy 56 bytes - perhaps 49 including APPro fields?

