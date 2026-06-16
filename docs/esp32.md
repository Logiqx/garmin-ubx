## Garmin + u-blox integration

### ESP32

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send summary data to APPro (or similar) once per second.

- Latitude + Longitude - 1 second average
- Speed (SOG) + Course (COG) - 1 second average
- sAcc + hAcc - error propagation should relate to latitude + longitude + SOG
- Number of satellites in use

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send UBX payloads 5 times per second.

- UBX payload - 20 byte array, suitable for the hardcoded Garmin [MTU](https://github.com/Logiqx/garmin-ubx/discussions/2) size

See the [protocol](protocol.md) page and [Google Sheet](https://docs.google.com/spreadsheets/d/1XAQbnhaoFV_E_tIXIC6ekw-PZZEP4lom5GkxAbc-hIk/edit?usp=sharing) for more details about the UBX payloads.

It might also be desirable to send things such as HR data from the Garmin to the ESP32.



#### Notes

The live data should use 1 second averages for SOG and COG, described in a [decimation](https://logiqx.github.io/gps-wizard/ideas/decimation/) article.

sAcc and hAcc should use applicable error propagation, so they relate to the 1 second averages.

The [protocol](protocol.md) page describes the recommended items for the UBX payload.

Various UBX data types have been reduced in size, but still remain suitable for speed sailing.



#### Higher Frequencies

10 Hz data would require a 240 byte payload, which is too large for the FIT record which has a limit of 256 bytes.

Two possible solutions would allow 10 Hz data to be logged:

1. Drop some of the legacy fields which have limited use - e.g. HDOP
2. Employ decimation to produce 5 Hz data - e.g. average every 2 items in the 10 Hz data

This isn't really a big issue though because the benefits of 10 Hz tend to be minimal.
