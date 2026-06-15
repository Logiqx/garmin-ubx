## Garmin + u-blox integration

### ESP32

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send data to APPro (or similar) once per second.

- Live data for use by APPro
  - Latitude + Longitude - 1 second average
  - Speed (SOG) + Course (COG) - 1 second average
  - sAcc + hAcc - error propagation should relate to latitude + longitude + SOG
  - Number of satellites in use
- UBX data for logging to the FIT
  - UBX definition - simple bit mask, representing the fields that are included in the payload
  - UBX records - typically 5 records, but could be fewer
  - UBX payload - 120 byte array for one seconds worth of 5 Hz data

See the [protocol](protocol.md) page and [Google Sheet](https://docs.google.com/spreadsheets/d/1XAQbnhaoFV_E_tIXIC6ekw-PZZEP4lom5GkxAbc-hIk/edit?usp=sharing) for more details about the UBX payloads.

It might also be desirable to send things such as HR data from the Garmin to the ESP32.



#### Notes

The live data should use 1 second averages for SOG and COG, described in a [decimation](https://logiqx.github.io/gps-wizard/ideas/decimation/) article.

sAcc and hAcc should use applicable error propagation, so they relate to the 1 second averages.

The [protocol](protocol.md) page describes the recommended items for the UBX payload.

Various UBX data types have been reduced in size, but still remain suitable for speed sailing.



#### 10 Hz / 20 Hz

10 Hz data would require a 240 byte payload, which is too large for the FIT record which has a limit of 256 bytes.

Two possible solutions would allow for higher rates to be logged:

1. Drop some of the legacy fields which have limited use - e.g. HDOP
2. Employ decimation to 5 Hz data - e.g. average every 2 items of 10 Hz data to produce 5 Hz data

This isn't really a big issue though because the benefits of 10 Hz tend to be minimal.
