## Garmin + u-blox integration

### Introduction

This project describes the integration of Garmin watches with u-blox GNSS receivers.

It initially sounds slightly crazy, but there are numerous benefits for the speed sailing community.

This page provides some background, describes the opportunities, and some of the benefits.



### Background

It is worth mentioning that after buying an expensive sports watch, serious runners and cyclists will often buy a heart rate monitor. The [HRM 600](https://www.garmin.com/en-GB/p/1473393/) is Garmin's premium heart rate chest strap, retailing at around 150 GBP / 170 USD. Nevertheless, numerous athletes will often pay for such a device, because it produces far more accurate readings than the HR sensor in their watch.

![garmin-hrm-600](img/garmin-hrm-600.jpg)

Serious speed sailors will typically use a dedicated GPS logger such as the [Motion GPS](https://www.motion-gps.com/motion) to record speeds that are far more accurate than a typical sports watch. GPS loggers such as the Mini Motion also record additional metrics that make it possible to validate the speed data, which is a capability sadly lacking in sports watches. The Garmin acts is a convenient device for monitoring performance, but the Motion provides accurate results.

![garmin-motion](img/garmin-motion.jpg)

After each session, speed sailors have to manually download the data from their Motion, prior to uploading to website such as [GPS-Speedsurfing.com](https://www.gps-speedsurfing.com/) and [speedsurf.app](https://speedsurf.app/). This is somewhat tedious when the Garmin watch can automatically upload to those same sites, plus other sites such as [Waterspeed](https://www.waterspeedapp.com/) and [Strava](https://www.strava.com/). There is no way to pair the Motion with Garmin watches and benefiting from automatic uploads to these platforms.



### Opportunities

Since the Motion is no longer being developed it is not possible to pair it up with a Garmin watch, but Scott Simms (developer of [APPro Windsurf](https://apps.garmin.com/en-US/apps/9567700b-6587-44be-9708-879bfc844791)) has been experimenting with [Bluetooth Low Energy](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) (BLE) connections between APPro Windsurf and a [LilyGo T-Display S3 AMOLED](https://lilygo.cc/products/t-display-s3-amoled?variant=43506902368437) (H713). The LilyGo is based on the ESP32-S3R8 Dual-core LX7 microprocessor, and the two devices can happily communicate via BLE.

![garmin-esp](img/garmin-esp.jpg)

There are several possible applications for BLE, but one idea is to link ESP32-based devices containing a u-blox GNSS chip with Garmin watches, via an open protocol. Several preliminary investigations and tests have been undertaken to establish the feasibility of this idea, and several benefits have been identified. These benefits are not limited to just the watch and APPro, but also apply to the ESP32-based device itself.



### Benefits

APPro

- All speeds and results displayed by APPro can be based on the u-blox data from the ESP32.
- Top speeds calculated from 5 Hz data will be far more reliable, and not prone to jitter or [aliasing](https://logiqx.github.io/gps-details/general/aliasing/).
- Metrics such as sAcc and hAcc can be used for filtering, thus eliminating the majority of spikes.

Garmin

- 5 Hz u-blox data can be recorded in the FIT file, alongside the usual session data.
- 5 Hz u-blox data can be automatically uploaded to Garmin Connect, essentially free storage.
- 5 Hz u-blox data can be automatically uploaded to GPS-Speedsurfing, Waterspeed, etc.

ESP32

- u-blox results can be displayed by APPro, especially useful for ESP32 devices without a screen.
- 5 Hz u-blox data can be automatically uploaded to Garmin Connect, essentially free storage.
- 5 Hz u-blox data can be automatically uploaded to GPS-Speedsurfing, Waterspeed, etc.

Analysis

- FIT files containing 5 Hz u-blox data, additional to the lat + lon + sog + cog from the built in GNSS.
- FIT files including 5 Hz u-blox accuracy estimates (e.g. sAcc and hAcc), plus satellite counts and HDOP.
- FIT files containing all of the usual session details, plus fitness data such as HR, etc.



### Feasibility

Some provisional investigations have already been undertaken.

- We have confirmed that two way communication is possible between APPro and an ESP device.
- We have confirmed that 5 Hz u-blox data can be stored in Garmin FIT files.

Some further investigations are required.

- Ability to log u-blox data without any oddities due to the timing of events, etc.
- Reliability of BLE connection throughout the whole session, during wipeouts, etc.



### Technical Details

The following pages discuss the technical details.

- [Protocol](protocol.md)
- [ESP32](esp32.md)
- [APPro](appro.md)



### Suitable Devices

There are a number of ESP32 devices that would benefit from this functionality.

- [ESP-GPS Logger](https://github.com/RP6conrad/ESP-GPS-Logger)
- [LISA GPS](http://lisawindsurfing.shop/products/lisa-watersports-gps)
- ... other ESP32 projects that are in progress, both with and without screens

Some benefits for users of ESP32 devices, even if it has a screen:

- Live results can also be displayed by APPro, providing additional real-time feedback.
- 5 Hz u-blox data can be automatically uploaded to Garmin Connect, essentially free storage.
- 5 Hz u-blox data can be automatically uploaded to GPS-Speedsurfing, Waterspeed, etc.

Automatic uploads to multiple platforms (courtesy of Garmin) is likely to make life much simpler for users.

Garmin watches supporting CIQ 3.1 upwards should be suitable, even $30 watches from eBay or Marketplace!



### Next Steps

Further activities to prove that this concept will work reliably.
