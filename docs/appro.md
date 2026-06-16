## Garmin + u-blox integration

### APPro

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send summary data to APPro (or similar) once per second.

- Latitude + Longitude - 1 second average
- Speed (SOG) + Course (COG) - 1 second average
- sAcc + hAcc - error propagation should relate to latitude + longitude + SOG
- Number of satellites in use

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send UBX payloads 5 times per second.

- UBX payload - 20 byte array, suitable for the hardcoded Garmin [MTU](https://github.com/Logiqx/garmin-ubx/discussions/2) size

See the [protocol](protocol.md) page and [Google Sheet](https://docs.google.com/spreadsheets/d/1XAQbnhaoFV_E_tIXIC6ekw-PZZEP4lom5GkxAbc-hIk/edit?usp=sharing) for more details about the UBX payloads.



#### Notes

- Live data from the ESP32 should be used instead of values from the Garmin API.
  - SOG and COG from the Garmin API should still be written to the FIT.
- The UBX payloads (and the definition) simply need to be written to the FIT.
  - 5 Hz data will be received as 5 payloads, each of 20 bytes




#### FIT Limitations

FIT records have a limit of 256 bytes, but the recommended UBX payload containing 5 Hz data is is only 100 bytes.

An earlier test confirmed that a 120 byte array can easily be stored in each FIT record.

![fit-test](img/fit-test.jpg)



#### Nuances

Garmin watches write records to the FIT file once per second, but the timings of BLE events may vary slightly.

To ensure that all payloads are handled correctly (e.g. live results, and FIT writer), [double-buffering](https://en.wikipedia.org/wiki/Multiple_buffering) is proposed.

- BLE event writes to buffer 1, whilst a timer event (every second) reads from buffer 2 (initially empty)

- BLE event writes to buffer 2, whilst a timer event (every second) reads from buffer 1
- ... etc

The buffers might benefit from using [Application.Storage](https://developer.garmin.com/connect-iq/core-topics/persisting-data/) but other approaches may be possible.

Hopefully this simple approach will ensure that all UBX payloads are safely written to the FIT file.

