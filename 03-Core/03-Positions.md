# Positions

The `UnlPositionData` and `ImprovedPostionData` classes provide a comprehensive representation of geographical and movement data for GPS-based systems. They include details like coordinates, speed, altitude, direction, and accuracy, along with road-related metadata such as speed limits and modifiers. With robust support for position quality assessment and timestamped data, it is well-suited for navigation and sensor-driven applications.

> 💡 **Tip**
>
> Do not confuse `UnlCoordinates` with `Position`. The `UnlCoordinates` class represents a geographic location using latitude, longitude, and altitude, and is used widely throughout the SDK. In contrast, `UnlPositionData` and `ImprovedPostionData` carry additional sensor data and are primarily used to represent the user's current location and movement.

## Instantiating Positions[​](#instantiating-positions "Direct link to Instantiating Positions")

The `UnlPositionData` class can be instantiated using the provided methods within the `SenseDataFactory` class. Additionally, it can be accessed through the methods exposed by the Navigation SDK for Android. For more details, refer to the [Get Started with Positioning](/docs/android/guides/positioning/get-started-positioning.md) guide.

## Raw position data[​](#raw-position-data "Direct link to Raw position data")

Raw position data represents unprocessed data from the GPS sensors of devices. It provides basic information. It corresponds to the `UnlPositionData` interface.

## Map matched position data[​](#map-matched-position-data "Direct link to Map matched position data")

Map matching is a method in location-based services that aligns raw GPS data with a digital map, correcting inaccuracies by snapping the position to the nearest logical location, such as roads. It corresponds with the `ImprovedPostionData` interface.

## Raw position data vs map matched position data[​](#raw-position-data-vs-map-matched-position-data "Direct link to Raw position data vs map matched position data")

The Map Matched positions provide more information, as it can be seen in the table below:

| Attribute            | Raw | Map Matched | When is available     | Description                                                                                                         |
| -------------------- | --- | ----------- | --------------------- | ------------------------------------------------------------------------------------------------------------------- |
| acquisitionTimestamp | ✅  | ✅          | always                | The system time when the data was collected from sensors.                                                           |
| satelliteTime        | ✅  | ✅          | always                | The satellite timestamp when position was collected by the sensors.                                                 |
| provider             | ✅  | ✅          | always                | The provider type (GPS, network, unknown)                                                                           |
| latitude & longitude | ✅  | ✅          | hasCoordinates        | The latitude and longitude at the position in degrees                                                               |
| altitude             | ✅  | ✅          | hasAltitude           | The altitude at the given position. Might be negative                                                               |
| speed                | ✅  | ✅          | hasSpeed              | The current speed (always non-negative)                                                                             |
| speedAccuracy        | ✅  | ✅          | hasSpeedAccuracy      | The current speed accuracy (always non-negative). Typical accuracy is 2 m/s in good conditions                      |
| course               | ✅  | ✅          | hasCourse             | The current direction of movement in degrees (0 north, 90 east, 180 south, 270 west)                                |
| courseAccuracy       | ✅  | ✅          | hasCourseAccuracy     | The current heading accuracy is degrees (typical accuracy is 25 degrees)                                            |
| horizontalAccuracy   | ✅  | ✅          | hasHorizontalAccuracy | The horizontal accuracy in meters. Always positive. (typical accuracy 5-20 meters)                                  |
| verticalAccuracy     | ✅  | ✅          | hasVerticalAccuracy   | The vertical accuracy in meters. Always positive.                                                                   |
| fixQuality           | ✅  | ✅          | always                | The accuracy quality (inertial – based on extrapolation, low – inaccurate, high – good accuracy, invalid – unknown) |
| coordinates          | ✅  | ✅          | hasCoordinates        | The coordinates of the position                                                                                     |
| roadModifier         | ❌  | ✅          | hasRoadLocalization   | The road modifiers (such as tunnel, bridge, ramp, etc.)                                                             |
| roadSpeedLimit       | ❌  | ✅          | always                | The speed limit on the current road in m/s. It is 0 if no speedLimit information is available                       |

> 📝 **Note**
>
>The `roadSpeedLimit` field may not always have a value, even if the position is map matched. This can happen if data is unavailable for the current road segment or if the position is not on a road. In such cases, the `roadSpeedLimit` field will be set to 0.

> 💡 **Tip**
>
> One common use case for `speed` and `roadSpeedLimit` is to check if a user is exceeding the legal speed limit. The `UnlAlarmService` class offers a reliable solution for this scenario. Refer to the [speed warnings guide](/01-Overview/04-Todo.md) for more details.
