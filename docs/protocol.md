## Garmin + u-blox integration

### Protocol

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

The [ESP32](esp32.md) and [APPro](appro.md) each have their own unique roles to play in the handling of the data.

It might also be desirable to send things such as HR data from the Garmin to the ESP32.



#### Recommended UBX Payload

The recommended UBX payload is 120 bytes per second, detailed in a [Google Sheet](https://docs.google.com/spreadsheets/d/1XAQbnhaoFV_E_tIXIC6ekw-PZZEP4lom5GkxAbc-hIk/edit?usp=sharing).

A quick summary, lacking the full detail that can be found in the Google Sheet:

| Name                             | Type | Bytes | Units |     Resolution      |
| -------------------------------- | :--: | :---: | :---: | :-----------------: |
| UTC Time \*                      | uint |   4   |   s   | 1 x 10<sup>-3</sup> |
| Latitude                         | int  |   4   |  deg  | 1 x 10<sup>-7</sup> |
| Longitude                        | int  |   4   |  deg  | 1 x 10<sup>-7</sup> |
| Speed (SOG) \*                   | uint |   2   |  m/s  | 1 x 10<sup>-3</sup> |
| Course (COG) \*                  | uint |   2   |  deg  | 1 x 10<sup>-2</sup> |
| Fix Type                         | uint |   1   |   -   |          -          |
| Satellites in Use                | uint |   1   |   -   |          -          |
| Horizontal Dilution of Precision | uint |   2   |   -   | 1 x 10<sup>-2</sup> |
| Horizontal Accuracy \*           | uint |   2   |   m   | 1 x 10<sup>-3</sup> |
| Speed Accuracy \*                | uint |   2   |  m/s  | 1 x 10<sup>-3</sup> |

These items are a superset of those in the [GPY](https://github.com/prichterich/compactgnss/blob/main/java/MinimalDataRecord.java) format, just adding horizontal accuracy.

Some of the items have been reduced in size (highlighted by asterisks), but are fine for speed sailing and other wind sports.

- UTC time - able to count in milliseconds for 50 days. Standard FIT timestamps provide the actual dates
- Speed and speed accuracy - reduced to 2 bytes for a ~65 m/s limit (around 130 kt), which is plenty for wind sports
- Course - reduced to 2 bytes for two decimal places, which should be sufficient for most purposes
- Horizontal accuracy - reduced to 2 bytes for a ~65 m limit, which is much higher than acceptable thresholds

The use values such as 65,635 can be regarded as [sentinel values](https://en.wikipedia.org/wiki/Sentinel_value) when limits are exceeded, similar to SDOS from Locosys.



#### Alternative UBX Payloads

Alternative payloads may be desirable, such as the inclusion of height above MSL, vertical accuracy, or heading accuracy.

A possible approach would be to use a simple bitmask to signify which items are present, and adhere to a fixed order for items.

This would be backwards and forwards compatible, allowing new fields to be added in the future, if required.

Note: This may require some kind of negotiation though, such as the ESP32 or APPro informing the other of the required bitmask.

| Name                             | Recommended? | Bitmask |
| -------------------------------- | :----------: | :-----: |
| Latitude                         |      Y       | 0x0001  |
| Longitude                        |      Y       | 0x0002  |
| Speed (SOG)                      |      Y       | 0x0004  |
| Course (COG)                     |      Y       | 0x0008  |
| Height above ellipsoid           |      -       | 0x0010  |
| Height above mean sea level      |      -       | 0x0020  |
| Fix Type                         |      Y       | 0x0040  |
| Satellites in Use                |      Y       | 0x0080  |
| Position Dilution of Precision   |      -       | 0x0100  |
| Horizontal Dilution of Precision |      Y       | 0x0200  |
| Horizontal Accuracy              |      Y       | 0x0400  |
| Vertical Accuracy                |      -       | 0x0800  |
| Speed Accuracy                   |      Y       | 0x1000  |
| Heading Accuracy                 |      -       | 0x2000  |
| Time Accuracy                    |      -       | 0x4000  |



### Notes

Some of the data types have been reduced in size, specifically for the speed sailing use case.

In the unlikely event of the types being insufficient, additional fields with the desired types can be incorporated into the protocol.

IMPORTANT: Additional items must be incorporated into the open protocol, not implemented independently.