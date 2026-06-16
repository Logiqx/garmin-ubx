## Garmin + u-blox integration

### Protocol

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send summary data to APPro (or similar) once per second.

- Latitude + Longitude - 1 second average
- Speed (SOG) + Course (COG) - 1 second average
- sAcc + hAcc - error propagation should relate to latitude + longitude + SOG
- Number of satellites in use

The BLE profile has yet to be defined, but it is envisaged that the ESP32 will send UBX payloads 5 times per second.

- UBX payload - 20 byte array, suitable for the hardcoded Garmin [MTU](https://github.com/Logiqx/garmin-ubx/discussions/2) size

The [ESP32](esp32.md) and [APPro](appro.md) each have their own unique roles to play in the handling of the data.

It might also be desirable to send things such as HR data from the Garmin to the ESP32.



#### UBX Payload

The UBX payload is exactly 20 bytes and the full details can be found in a [Google Sheet](https://docs.google.com/spreadsheets/d/1XAQbnhaoFV_E_tIXIC6ekw-PZZEP4lom5GkxAbc-hIk/edit?usp=sharing).

A quick summary, lacking the full detail that can be found in the Google Sheet:

| Name                             | Type | Bytes | Units |     Resolution      |
| -------------------------------- | :--: | :---: | :---: | :-----------------: |
| Latitude                         | int  |   4   |  deg  | 1 x 10<sup>-7</sup> |
| Longitude                        | int  |   4   |  deg  | 1 x 10<sup>-7</sup> |
| UTC Time                         | uint |   2   |   s   | 1 x 10<sup>-3</sup> |
| Speed over Ground (SOG)          | uint |   2   |  m/s  | 1 x 10<sup>-3</sup> |
| Course over Ground (COG)         | uint |   2   |  deg  | 1 x 10<sup>-2</sup> |
| Satellites in Use                | uint |   1   |   -   |          -          |
| Horizontal Dilution of Precision | uint |   1   |   -   | 1 x 10<sup>-1</sup> |
| Horizontal Accuracy              | uint |   2   |   m   | 1 x 10<sup>-3</sup> |
| Speed Accuracy                   | uint |   2   |  m/s  | 1 x 10<sup>-3</sup> |

These items are similar to those in the [GPY](https://github.com/prichterich/compactgnss/blob/main/java/MinimalDataRecord.java) format, just replacing fix type with horizontal accuracy.

- Fix type can be inferred from the number of satellites in use (e.g. 3 sats = 2D, 4 sats = 3D)
- Horizontal accuracy can be very useful when assessing the veracity of UBX data

Some items have been reduced in size, but are perfectly fine for speed sailing and other wind sports.

- UTC time - able to count in milliseconds for 65 seconds. Standard FIT timestamps provide date + time
- Speed - reduced to 2 bytes for a ~65 m/s limit (around 130 kt), which is plenty for wind sports
- Course - reduced to 2 bytes for two decimal places, which should be sufficient for most purposes
- Horizontal Dilution of Precision - one decimal place is fine, because hAcc and sAcc are much more useful
- Horizontal accuracy - reduced to 2 bytes for a ~65 m limit, which is much higher than acceptable thresholds
- Speed accuracy - reduced to 2 bytes for a ~65 m/s limit (around 130 kt), which is plenty for wind sports

The use values such as 65,635 can be regarded as [sentinel values](https://en.wikipedia.org/wiki/Sentinel_value) when limits are exceeded, similar to SDOS from Locosys.



### Notes

The the MTU size for the Garmin BLE stack is [hardcoded to 23 bytes](https://forums.garmin.com/developer/connect-iq/f/discussion/196823/bluetooth-low-energy-mtu-size-for-characteristics), so just 20 bytes for actual data.

Some of the data types have therefore been reduced in size, specifically for the speed sailing use case.

A custom chunking protocol could be implemented, but for simplicity the UBX payloads are 20 bytes.

