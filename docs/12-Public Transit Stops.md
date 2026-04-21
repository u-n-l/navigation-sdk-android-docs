# Public Transit stops

This API provides detailed access to public transport data including agencies, routes, stops, and trips. It integrates with interactive map-based applications using the UNL Navigation SDK and allows developers to dynamically fetch and explore real-time public transportation information from selected positions on the map.

> 💡 **TIP**
>
> The structure of the public transport data is modeled after the [General Transit Feed Specification (GTFS)](https://gtfs.org/documentation/schedule/reference/) and offers access to a subset of the fields and entities defined in GTFS.

## Key Features[​](#key-features "Direct link to Key Features")

* Query public transport overlays by screen position
* Retrieve structured information about transport agencies, stops, routes, and trips
* Support for real-time data including delays and cancellations
* Metadata about accessibility, bike allowances, and platform details
* Filter trips by route type, route short name, or agency

## How It Works[​](#how-it-works "Direct link to How It Works")

Set a cursor position on the map and query for public transport overlay items at that location. Each overlay item provides access to detailed stop information including agencies, routes, and trip schedules.

* Kotlin
* Java

```kotlin
// Kotlin
mapView.onLongDown = { screenPosition ->
    // Set cursor position on the screen
    mapView.cursorScreenPosition = screenPosition

    // Get the public transit overlay items at that position
    val overlayItems = mapView.cursorSelectionOverlayItems

    overlayItems?.forEach { overlayItem ->
        // Check if this is a public transit overlay
        if (overlayItem.overlayUid == ECommonOverlayId.PublicTransport.value) {
            // Get the preview extended data
            val previewDataList = GemList<Parameter>()

            overlayItem.getPreviewExtendedData(
                previewDataList,
                UnlProgressListener.create(
                    onCompleted = { errorCode ->
                        if (errorCode == UnlError.NoError) {
                            // Process the public transit data
                            previewDataList.forEach { parameter ->
                                Log.d("PT", "${parameter.key}: ${parameter.valueString}")
                            }
                        }
                    }
                )
            )
        }
    }
}

```

```java
// Java
mapView.setOnLongDown(screenPosition -> {
    // Set cursor position on the screen
    mapView.setCursorScreenPosition(screenPosition);

    // Get the public transit overlay items at that position
    List<OverlayItem> overlayItems = mapView.getCursorSelectionOverlayItems();

    if (overlayItems != null) {
        for (OverlayItem overlayItem : overlayItems) {
            // Check if this is a public transit overlay
            if (overlayItem.getOverlayUid() == ECommonOverlayId.PublicTransport.getValue()) {
                // Get the preview extended data
                GemList<Parameter> previewDataList = new GemList<>();

                overlayItem.getPreviewExtendedData(
                    previewDataList,
                    UnlProgressListener.create(
                        errorCode -> {
                            if (errorCode == UnlError.NoError) {
                                // Process the public transit data
                                for (Parameter parameter : previewDataList) {
                                    Log.d("PT", parameter.getKey() + ": " + parameter.getValueString());
                                }
                            }
                        }
                    )
                );
            }
        }
    }
});

```

> 💡 **TIP**
>
> You can also obtain public transit overlay items by performing an overlay search using `ECommonOverlayId.PublicTransport`. Once you retrieve the corresponding `OverlayItem`s, use their `getPreviewExtendedData` method to access the stop information.
>
> See the [Search on overlays](/06-Search/02-Get%20Started%20with%20Search.md#search-on-overlays) guide for more details.

> 🚨 **DANGER**
>
> All returned times are local times represented as Unix timestamps in seconds.
>
> Use the `TimeService` to convert them to other time zones.

> 🚨 **DANGER**
>
>There are two types of public transit stops on the map:
>
>* Stops which are of type `OverlayItem` and can be selected via the `cursorSelectionOverlayItems` property. These stops provide extensive details accessible through `getPreviewExtendedData`. They are displayed with a blue icon when using the default style.
>* Stops which are of type `UnlLandmark` and can be selected via the `cursorSelectionLandmarks` property. These stops do not provide extensive details. They are displayed with a gray icon when using the default style.

## Working with Public Transit Data[​](#working-with-public-transit-data "Direct link to Working with Public Transit Data")

The public transit data is provided as a list of `Parameter` objects. Each parameter contains a key-value pair representing different aspects of the transit stop information.

### Accessing Stop Information[​](#accessing-stop-information "Direct link to Accessing Stop Information")

* Kotlin
* Java

```kotlin
// Kotlin
val previewDataList = GemList<Parameter>()

overlayItem.getPreviewExtendedData(
    previewDataList,
    UnlProgressListener.create(
        onCompleted = { errorCode ->
            if (errorCode == UnlError.NoError) {
                previewDataList.forEach { parameter ->
                    when (parameter.key) {
                        EPublicTransitOverlayParamsKeys.StopId.value -> {
                            val stopId = parameter.valueLong
                            Log.d("PT", "Stop ID: $stopId")
                        }
                        EPublicTransitOverlayParamsKeys.StopName.value -> {
                            val stopName = parameter.valueString
                            Log.d("PT", "Stop Name: $stopName")
                        }
                        EPublicTransitOverlayParamsKeys.AgencyId.value -> {
                            val agencyId = parameter.valueLong
                            Log.d("PT", "Agency ID: $agencyId")
                        }
                        EPublicTransitOverlayParamsKeys.AgencyName.value -> {
                            val agencyName = parameter.valueString
                            Log.d("PT", "Agency Name: $agencyName")
                        }
                    }
                }
            }
        }
    )
)

```

```java
// Java
GemList<Parameter> previewDataList = new GemList<>();

overlayItem.getPreviewExtendedData(
    previewDataList,
    UnlProgressListener.create(
        errorCode -> {
            if (errorCode == UnlError.NoError) {
                for (Parameter parameter : previewDataList) {
                    String key = parameter.getKey();
                    if (key.equals(EPublicTransitOverlayParamsKeys.StopId.getValue())) {
                        long stopId = parameter.getValueLong();
                        Log.d("PT", "Stop ID: " + stopId);
                    } else if (key.equals(EPublicTransitOverlayParamsKeys.StopName.getValue())) {
                        String stopName = parameter.getValueString();
                        Log.d("PT", "Stop Name: " + stopName);
                    } else if (key.equals(EPublicTransitOverlayParamsKeys.AgencyId.getValue())) {
                        long agencyId = parameter.getValueLong();
                        Log.d("PT", "Agency ID: " + agencyId);
                    } else if (key.equals(EPublicTransitOverlayParamsKeys.AgencyName.getValue())) {
                        String agencyName = parameter.getValueString();
                        Log.d("PT", "Agency Name: " + agencyName);
                    }
                }
            }
        }
    )
);

```

## Parameter Keys[​](#parameter-keys "Direct link to Parameter Keys")

The following parameter keys are available for public transit data:

### Agency Information[​](#agency-information "Direct link to Agency Information")

| Parameter Key                                | Type     | Description                     |
| -------------------------------------------- | -------- | ------------------------------- |
| `EPublicTransitOverlayParamsKeys.AgencyId`   | `Long`   | Agency ID                       |
| `EPublicTransitOverlayParamsKeys.AgencyName` | `String` | Full name of the transit agency |
| `EPublicTransitOverlayParamsKeys.AgencyURL`  | `String` | URL of the transit agency       |

### Route Information[​](#route-information "Direct link to Route Information")

| Parameter Key                                    | Type     | Description                                   |
| ------------------------------------------------ | -------- | --------------------------------------------- |
| `EPublicTransitOverlayParamsKeys.RouteId`        | `Long`   | UnlRoute ID                                      |
| `EPublicTransitOverlayParamsKeys.RouteShortName` | `String` | Short name of a route (e.g., "32", "100X")    |
| `EPublicTransitOverlayParamsKeys.RouteLongName`  | `String` | Full name of a route with destination or stop |
| `EPublicTransitOverlayParamsKeys.RouteType`      | `Int`    | Type of route (see `EUnlRouteType`)              |
| `EPublicTransitOverlayParamsKeys.RouteColor`     | `String` | UnlRoute color designation                       |
| `EPublicTransitOverlayParamsKeys.RouteTextColor` | `String` | Text color for route                          |
| `EPublicTransitOverlayParamsKeys.RouteHeading`   | `String` | Optional heading information                  |

### Stop Information[​](#stop-information "Direct link to Stop Information")

| Parameter Key                                       | Type            | Description                                      |
| --------------------------------------------------- | --------------- | ------------------------------------------------ |
| `EPublicTransitOverlayParamsKeys.StopId`            | `Long`          | Stop/platform, station, entrance/exit identifier |
| `EPublicTransitOverlayParamsKeys.StopName`          | `String`        | Name of the location                             |
| `EPublicTransitOverlayParamsKeys.StopTimeLatitude`  | `ParameterList` | Latitude coordinate of the stop                  |
| `EPublicTransitOverlayParamsKeys.StopTimeLongitude` | `Double`        | Longitude coordinate of the stop                 |
| `EPublicTransitOverlayParamsKeys.IsStation`         | `Boolean`       | Whether this location is a station               |
| `EPublicTransitOverlayParamsKeys.Routes`            | `ParameterList` | Associated routes for the stop                   |

### Trip Information[​](#trip-information "Direct link to Trip Information")

| Parameter Key                                              | Type            | Description                                                |
| ---------------------------------------------------------- | --------------- | ---------------------------------------------------------- |
| `EPublicTransitOverlayParamsKeys.TripIndex`                | `Long`          | Trip index                                                 |
| `EPublicTransitOverlayParamsKeys.TripDate`                 | `Long`          | Date of the trip (Unix timestamp in seconds)               |
| `EPublicTransitOverlayParamsKeys.TripDepartureTime`        | `Long`          | Departure time from first stop (Unix timestamp in seconds) |
| `EPublicTransitOverlayParamsKeys.TripHasRealtime`          | `Boolean`       | Whether real-time data is available                        |
| `EPublicTransitOverlayParamsKeys.TripIsCancelled`          | `Boolean`       | Whether the trip is cancelled                              |
| `EPublicTransitOverlayParamsKeys.TripDelayMinutes`         | `Int`           | Delay in minutes                                           |
| `EPublicTransitOverlayParamsKeys.TripStopIndex`            | `Int`           | Stop index                                                 |
| `EPublicTransitOverlayParamsKeys.TripStopPlatformCode`     | `String`        | Platform code                                              |
| `EPublicTransitOverlayParamsKeys.TripWheelchairAccessible` | `Int`           | Wheelchair accessibility (see `EWheelchairAccessible`)     |
| `EPublicTransitOverlayParamsKeys.TripBikesAllowed`         | `Int`           | Bikes allowed status (see `EBikesAllowed`)                 |
| `EPublicTransitOverlayParamsKeys.TripAgencyId`             | `Long`          | Agency ID for this trip                                    |
| `EPublicTransitOverlayParamsKeys.TripStopTimes`            | `ParameterList` | List of stop times for this trip                           |
| `EPublicTransitOverlayParamsKeys.TripHasShape`             | `Boolean`       | Whether trip has shape data                                |

### Stop UnlTime Information[​](#stop-time-information "Direct link to Stop UnlTime Information")

| Parameter Key                                           | Type            | Description                                     |
| ------------------------------------------------------- | --------------- | ----------------------------------------------- |
| `EPublicTransitOverlayParamsKeys.StopTimeName`          | `String`        | Name of the serviced stop                       |
| `EPublicTransitOverlayParamsKeys.StopTimeLatitude`      | `ParameterList` | Latitude of the stop                            |
| `EPublicTransitOverlayParamsKeys.StopTimeLongitude`     | `Double`        | Longitude of the stop                           |
| `EPublicTransitOverlayParamsKeys.StopTimeHasRealTime`   | `Boolean`       | Whether data is provided in real-time           |
| `EPublicTransitOverlayParamsKeys.StopTimeDelay`         | `Int`           | Delay in seconds                                |
| `EPublicTransitOverlayParamsKeys.StopTimeDepartureTime` | `Long`          | Departure time (Unix timestamp in seconds)      |
| `EPublicTransitOverlayParamsKeys.StopTimeIsBefore`      | `Boolean`       | Whether stop time is before current time        |
| `EPublicTransitOverlayParamsKeys.StopTimeDetails`       | `Int`           | Stop details including wheelchair accessibility |

## Route Types[​](#route-types "Direct link to Route Types")

The `EUnlRouteType` enum represents different types of public transport.

| Enum Value       | Description                                                                                    |
| ---------------- | ---------------------------------------------------------------------------------------------- |
| `Bus`            | Bus, Trolleybus. Used for short and long-distance bus routes.                                  |
| `Underground`    | Subway, Metro. Any underground rail system within a metropolitan area.                         |
| `Railway`        | Rail. Used for intercity or long-distance travel.                                              |
| `Tram`           | Tram, Streetcar, Light rail. Any light rail or street level system within a metropolitan area. |
| `WaterTransport` | Water transport. Used for ferries and other water-based transit.                               |
| `Misc`           | Miscellaneous. Includes other types of public transport not covered by the other categories.   |

### Filtering by Route Type[​](#filtering-by-route-type "Direct link to Filtering by Route Type")

* Kotlin
* Java

```kotlin
// Kotlin
previewDataList.forEach { parameter ->
    if (parameter.key == EPublicTransitOverlayParamsKeys.RouteType.value) {
        when (parameter.valueLong.toInt()) {
            EUnlRouteType.Bus.value -> Log.d("PT", "Bus route")
            EUnlRouteType.Underground.value -> Log.d("PT", "Underground route")
            EUnlRouteType.Railway.value -> Log.d("PT", "Railway route")
            EUnlRouteType.Tram.value -> Log.d("PT", "Tram route")
            EUnlRouteType.WaterTransport.value -> Log.d("PT", "Water transport route")
            EUnlRouteType.Misc.value -> Log.d("PT", "Miscellaneous route")
        }
    }
}

```

```java
// Java
for (Parameter parameter : previewDataList) {
    if (parameter.getKey().equals(EPublicTransitOverlayParamsKeys.RouteType.getValue())) {
        int routeType = (int) parameter.getValueLong();
        if (routeType == EUnlRouteType.Bus.getValue()) {
            Log.d("PT", "Bus route");
        } else if (routeType == EUnlRouteType.Underground.getValue()) {
            Log.d("PT", "Underground route");
        } else if (routeType == EUnlRouteType.Railway.getValue()) {
            Log.d("PT", "Railway route");
        } else if (routeType == EUnlRouteType.Tram.getValue()) {
            Log.d("PT", "Tram route");
        } else if (routeType == EUnlRouteType.WaterTransport.getValue()) {
            Log.d("PT", "Water transport route");
        } else if (routeType == EUnlRouteType.Misc.getValue()) {
            Log.d("PT", "Miscellaneous route");
        }
    }
}

```

## Wheelchair Accessibility[​](#wheelchair-accessibility "Direct link to Wheelchair Accessibility")

The `EWheelchairAccessible` enum indicates wheelchair accessibility information.

| Enum Value | Description                                                |
| ---------- | ---------------------------------------------------------- |
| `NoInfo`   | No accessibility information for the trip                  |
| `Yes`      | Vehicle can accommodate at least one rider in a wheelchair |
| `No`       | No riders in wheelchairs can be accommodated on this trip  |

## Bikes Allowed[​](#bikes-allowed "Direct link to Bikes Allowed")

The `EBikesAllowed` enum indicates whether bikes are allowed.

| Enum Value | Description                                  |
| ---------- | -------------------------------------------- |
| `NoInfo`   | No bike information for the trip             |
| `Yes`      | Vehicle can accommodate at least one bicycle |
| `No`       | No bicycles are allowed on this trip         |
