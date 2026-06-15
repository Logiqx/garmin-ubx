## Garmin + u-blox integration

### Analysis

The FIT file will containing UBX payloads within the main records.

It should be a relatively straightforward process to extract the individual items from the UBX payloads.

See the [protocol](protocol.md) page and [Google Sheet](https://docs.google.com/spreadsheets/d/1XAQbnhaoFV_E_tIXIC6ekw-PZZEP4lom5GkxAbc-hIk/edit?usp=sharing) for more details about the UBX payloads.



### Additional Thoughts

Since the data from the built-in GNSS receiver is also in the FIT file there may be further possibilities to verify results.

The FIT file essentially contains the 1 Hz Garmin data, and 5 Hz UBX data which can be considered to be independent.