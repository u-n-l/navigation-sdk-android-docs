# Advanced features

## Compute route ranges[​](#compute-route-ranges "Direct link to Compute route ranges")

In order to compute a route range we need to:

* Specify in the `UnlRoutePreferences` the most important route preferences (others can also be used):

  <!-- -->

  * `routeRanges` list containing a list of range values, one for each route we compute. Measurement units are corresponding to the specified `routeType` (see the table below)
  * \[optional] `transportMode` (by default `EUnlRouteTransportMode.Car`)
  * \[optional] `routeType` (can be `EUnlRouteType.Fastest`, `EUnlRouteType.Economic`, `EUnlRouteType.Shortest` - by default is fastest)
  * \[optional] `routeRangesQuality` ( a value in the interval \[0, 100], default 100) representing the quality of the generated polygons.

* The list of landmarks will contain only one landmark, the starting point for the route range computation.

| Preference | Measurement unit |
| ---------- | ---------------- |
| fastest    | seconds          |
| shortest   | meters           |
| economic   | Wh               |

> 🚨 **DANGER**
>
> Routes computed using route ranges are **not navigable**.

> 🚨 **DANGER**
>
> The `EUnlRouteType.Scenic` route type is not supported for route ranges.

Route can be computed with a code like the following. It is a range route computation because it only has a simple `UnlLandmark` and `routeRanges` contains values (in this case 2 routes will be computed).

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    // Define the departure.
    val startLandmark = UnlLandmark("Start", 48.85682, 2.34375)

    // Define the route preferences.
    // Compute 2 ranges, 30 min and 60 min
    val routePreferences = UnlRoutePreferences().apply {
        routeType = EUnlRouteType.Fastest
        setRouteRanges(arrayListOf(1800, 3600), 100) // quality = 100
    }

    val routingService = UnlRoutingService(
        preferences = routePreferences,
        onCompleted = { routes, errorCode, hint ->
            when (errorCode) {
                UnlError.NoError -> {
                    // Route range computed successfully
                    // Display routes on map using the additional fill color setting
                    mapView?.presentRoutes(routes)
                }
                UnlError.Cancel -> {
                    // Route computation canceled
                }
                else -> {
                    // Error occurred
                    showDialog("Error: ${UnlError.getMessage(errorCode)}")
                }
            }
        }
    )

    routingService.calculateRoute(arrayListOf(startLandmark))
}

```

```java
// Java
SdkCall.execute(() -> {
    // Define the departure.
    UnlLandmark startLandmark = new UnlLandmark("Start", 48.85682, 2.34375);

    // Define the route preferences.
    // Compute 2 ranges, 30 min and 60 min
    UnlRoutePreferences routePreferences = new UnlRoutePreferences();
    routePreferences.setRouteType(EUnlRouteType.Fastest);
    routePreferences.setRouteRanges(new ArrayList<>(Arrays.asList(1800, 3600)), 100); // quality = 100

    UnlRoutingService routingService = new UnlRoutingService(
        routePreferences,
        (routes, errorCode, hint) -> {
            if (errorCode == UnlError.NoError) {
                // Route range computed successfully
                // Display routes on map using the additional fill color setting
                if (mapView != null) {
                    mapView.presentRoutes(routes);
                }
            } else if (errorCode == UnlError.Cancel) {
                // Route computation canceled
            } else {
                // Error occurred
                showDialog("Error: " + UnlError.getMessage(errorCode));
            }
        }
    );

    routingService.calculateRoute(new ArrayList<>(Collections.singletonList(startLandmark)));
});

```

> 📝 **INFO**
>
> The computed routes can be displayed on the map, just like any regular route, with the only difference that the additional settings for polygon fill color can be configured in the route render settings.

## Compute path based routes[​](#compute-path-based-routes "Direct link to Compute path based routes")

A `UnlPath` is a structure containing a list of coordinates (a track). It can be created based on:

* custom coordinates specified by the user
* coordinates recorded in a GPX file
* coordinates obtained by doing a finger draw on the map

A **Path backed landmark** is a special kind of `UnlLandmark` that has a `UnlPath` inside it.

Sometimes we want to compute routes based on a list of one or more **Path backed landmark**(s) and optionally some regular `UnlLandmark`(s). In this case the result will only contain one route. The path provided as waypoint track is used as a hint for the routing algorithm.

You can see an example below (the highlighted area represents the code necessary to create the list with one element of type landmark built based on a path):

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    val coords = arrayListOf(
        UnlCoordinates(40.786, -74.202),
        UnlCoordinates(40.690, -74.209),
        UnlCoordinates(40.695, -73.814),
        UnlCoordinates(40.782, -73.710)
    )

    val gemPath = UnlPath.produceWithCoords(coords)

    // A list containing only one Path backed Landmark
    val landmarkList = arrayListOf(gemPath?.toLandmark())

    // Define the route preferences.
    val routePreferences = UnlRoutePreferences()

    val routingService = UnlRoutingService(
        preferences = routePreferences,
        onCompleted = { routes, errorCode, hint ->
            when (errorCode) {
                UnlError.NoError -> {
                    // Route computed successfully
                    showSnackbar("Number of routes: ${routes.size}")
                }
                UnlError.Cancel -> {
                    // Route computation canceled
                }
                else -> {
                    // Error occurred
                    showDialog("Error: ${UnlError.getMessage(errorCode)}")
                }
            }
        }
    )

    routingService.calculateRoute(landmarkList.filterNotNull() as ArrayList<UnlLandmark>)
}

```

```java
// Java
SdkCall.execute(() -> {
    ArrayList<UnlCoordinates> coords = new ArrayList<>();
    coords.add(new UnlCoordinates(40.786, -74.202));
    coords.add(new UnlCoordinates(40.690, -74.209));
    coords.add(new UnlCoordinates(40.695, -73.814));
    coords.add(new UnlCoordinates(40.782, -73.710));

    UnlPath gemPath = UnlPath.produceWithCoords(coords);

    // A list containing only one UnlPath backed UnlLandmark
    ArrayList<UnlLandmark> landmarkList = new ArrayList<>();
    if (gemPath != null) {
        landmarkList.add(gemPath.toLandmark());
    }

    // Define the route preferences.
    UnlRoutePreferences routePreferences = new UnlRoutePreferences();

    UnlRoutingService routingService = new UnlRoutingService(
        routePreferences,
        (routes, errorCode, hint) -> {
            if (errorCode == UnlError.NoError) {
                // Route computed successfully
                showSnackbar("Number of routes: " + routes.size());
            } else if (errorCode == UnlError.Cancel) {
                // Route computation canceled
            } else {
                // Error occurred
                showDialog("Error: " + UnlError.getMessage(errorCode));
            }
        }
    );

    routingService.calculateRoute(landmarkList);
});

```

> 💡 **TIP**
>
> The `UnlPath` object associated to a path based landmark can be modified using the `trackData` property available on the `UnlLandmark` object. See the [Landmarks guide](/03-Core/04-Landmarks.md) for more details about this.

> 🚨 **DANGER**
>
> When computing a route based on a path backed landmark **and** non-path backed landmarks, it is mandatory to set the `accurateTrackMatch` field from `UnlRoutePreferences` to `true`. Otherwise, the routing computation will fail with a `UnlError.Unsupported` error.

The `isTrackResume` field from `UnlRoutePreferences` can also be set to configure the behaviour of the routing engine when one track based landmark is used as a waypoint together with other landmarks. If this field is set to `true`, the routing engine will try to match the entire track of the path based landmark. Otherwise, if set to `false`, only the end point of the track will be used as waypoints.

## Computing a route based on a GPX file[​](#computing-a-route-based-on-a-gpx-file "Direct link to Computing a route based on a GPX file")

You can compute a route based on a GPX file by using the `path based landmark` described in the previous section. The only difference is how we compute the `gemPath`.

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    // Load GPX file from assets
    val gpxAssetsFilename = "gpx/recorded_route.gpx"
    val inputStream = applicationContext.resources.assets.open(gpxAssetsFilename)

    // Produce a Path based on the GPX data
    val gemPath = UnlPath.produceWithGpx(inputStream)

    gemPath?.let { path ->
        // UnlLandmarkList will contain only one path based landmark.
        val landmarkList = arrayListOf(path.toLandmark())

        // Define the route preferences.
        val routePreferences = UnlRoutePreferences().apply {
            transportMode = EUnlRouteTransportMode.Bicycle
        }

        val routingService = UnlRoutingService(
            preferences = routePreferences,
            onCompleted = { routes, errorCode, hint ->
                when (errorCode) {
                    UnlError.NoError -> {
                        // Handle successful route calculation
                        mapView?.presentRoutes(routes)
                    }
                    UnlError.Cancel -> {
                        // Route computation canceled
                    }
                    else -> {
                        // Error occurred
                        showDialog("Error: ${UnlError.getMessage(errorCode)}")
                    }
                }
            }
        )

        routingService.calculateRoute(landmarkList)
    } ?: run {
        showDialog("GPX file could not be loaded")
    }
}

```

```java
// Java
SdkCall.execute(() -> {
    // Load GPX file from assets
    String gpxAssetsFilename = "gpx/recorded_route.gpx";
    InputStream inputStream = getApplicationContext().getResources().getAssets().open(gpxAssetsFilename);

    // Produce a Path based on the GPX data
    UnlPath gemPath = UnlPath.produceWithGpx(inputStream);

    if (gemPath != null) {
        // UnlLandmarkList will contain only one path based landmark.
        ArrayList<UnlLandmark> landmarkList = new ArrayList<>();
        landmarkList.add(gemPath.toLandmark());

        // Define the route preferences.
        UnlRoutePreferences routePreferences = new UnlRoutePreferences();
        routePreferences.setTransportMode(EUnlRouteTransportMode.Bicycle);

        UnlRoutingService routingService = new UnlRoutingService(
            routePreferences,
            (routes, errorCode, hint) -> {
                if (errorCode == UnlError.NoError) {
                    // Handle successful route calculation
                    if (mapView != null) {
                        mapView.presentRoutes(routes);
                    }
                } else if (errorCode == UnlError.Cancel) {
                    // Route computation canceled
                } else {
                    // Error occurred
                    showDialog("Error: " + UnlError.getMessage(errorCode));
                }
            }
        );

        routingService.calculateRoute(landmarkList);
    } else {
        showDialog("GPX file could not be loaded");
    }
});

```

## Finger drawn path[​](#finger-drawn-path "Direct link to Finger drawn path")

When necessary, it is possible to record a path based on drawing with the finger on the map.

It is also possible to record multiple paths. In this situation a straight line is added between any 2 consecutive finger drawn paths.

When you want to enter this recording mode:

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    mapView?.enableDrawMarkersMode()
}

```

```java
// Java
SdkCall.execute(() -> {
    if (mapView != null) {
        mapView.enableDrawMarkersMode();
    }
});

```

When you want to exit this mode, you can get the generated `List<UnlLandmark>` with the following:

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    val landmarks = mapView?.disableDrawMarkersMode()

    landmarks?.let { landmarkList ->
        val routePreferences = UnlRoutePreferences().apply {
            accurateTrackMatch = false
            ignoreRestrictionsOverTrack = true
        }

        val routingService = UnlRoutingService(
            preferences = routePreferences,
            onCompleted = { routes, errorCode, hint ->
                when (errorCode) {
                    UnlError.NoError -> {
                        // Handle successful route calculation
                        mapView?.presentRoutes(routes)
                    }
                    UnlError.Cancel -> {
                        // Route computation canceled
                    }
                    else -> {
                        // Error occurred
                        showDialog("Error: ${UnlError.getMessage(errorCode)}")
                    }
                }
            }
        )

        routingService.calculateRoute(landmarkList)
    }
}

```

```java
// Java
SdkCall.execute(() -> {
    List<UnlLandmark> landmarks = mapView != null ? mapView.disableDrawMarkersMode() : null;

    if (landmarks != null) {
        UnlRoutePreferences routePreferences = new UnlRoutePreferences();
        routePreferences.setAccurateTrackMatch(false);
        routePreferences.setIgnoreRestrictionsOverTrack(true);

        UnlRoutingService routingService = new UnlRoutingService(
            routePreferences,
            (routes, errorCode, hint) -> {
                if (errorCode == UnlError.NoError) {
                    // Handle successful route calculation
                    if (mapView != null) {
                        mapView.presentRoutes(routes);
                    }
                } else if (errorCode == UnlError.Cancel) {
                    // Route computation canceled
                } else {
                    // Error occurred
                    showDialog("Error: " + UnlError.getMessage(errorCode));
                }
            }
        );

        routingService.calculateRoute(new ArrayList<>(landmarks));
    }
});

```

The resulted `List<UnlLandmark>` will only contain one element, a path based `UnlLandmark`.

## Compute public transit routes[​](#compute-public-transit-routes "Direct link to Compute public transit routes")

In order to compute a public transit route we need to set the `transportMode` field in the `UnlRoutePreferences` like this:

* Kotlin
* Java

```kotlin
// Kotlin
// Define the route preferences with public transport mode.
val routePreferences = UnlRoutePreferences().apply {
    transportMode = EUnlRouteTransportMode.Public
}

```

```java
// Java
// Define the route preferences with public transport mode.
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
routePreferences.setTransportMode(EUnlRouteTransportMode.Public);

```

> 🚨 **DANGER**
>
> Public transit routes are not navigable.

The full source code to compute a public transit route and handle it could look like this:

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    // Define the departure.
    val departureLandmark = UnlLandmark("Departure", 45.6646, 25.5872)

    // Define the destination.
    val destinationLandmark = UnlLandmark("Destination", 45.6578, 25.6233)

    // Define the route preferences with public transport mode.
    val routePreferences = UnlRoutePreferences().apply {
        transportMode = EUnlRouteTransportMode.Public
    }

    val routingService = UnlRoutingService(
        preferences = routePreferences,
        onCompleted = { routes, errorCode, hint ->
            when (errorCode) {
                UnlError.NoError -> {
                    if (routes.isNotEmpty()) {
                        // Get the routes collection from map preferences.
                        val routesMap = mapView?.preferences?.routes

                        // Display the routes on map.
                        routes.forEachIndexed { index, route ->
                            routesMap?.add(route, index == 0, if (index == 0) "Route" else null)
                        }

                        // Convert normal route to UnlPTRoute
                        val ptRoute = routes.first().toPTRoute()

                        // Convert each segment to UnlPTRouteSegment
                        val ptSegments = ptRoute?.segments?.map { segment ->
                            segment.toPTRouteSegment()
                        }

                        ptSegments?.forEach { segment ->
                            segment?.let { ptSegment ->
                                val transitType = ptSegment.transitType

                                if (ptSegment.isCommon()) { // PT segment
                                    val ptInstructions = ptSegment.instructions?.map { instruction ->
                                        instruction.toPTRouteInstruction()
                                    }
                                    ptInstructions?.forEach { ptInstr ->
                                        ptInstr?.let { instruction ->
                                            // handle public transit instruction
                                            val stationName = instruction.name
                                            val departure = instruction.departureTime
                                            val arrival = instruction.arrivalTime
                                            // ...
                                        }
                                    }
                                } else { // walk segment
                                    val instructions = ptSegment.instructions
                                    instructions?.forEach { walkInstr ->
                                        // handle walk instruction
                                    }
                                }
                            }
                        }
                    }
                }
                UnlError.Cancel -> {
                    // Route computation canceled
                }
                else -> {
                    // Error occurred
                    showDialog("Error: ${UnlError.getMessage(errorCode)}")
                }
            }
        }
    )

    routingService.calculateRoute(arrayListOf(departureLandmark, destinationLandmark))
}

```

```java
// Java
SdkCall.execute(() -> {
    // Define the departure.
    UnlLandmark departureLandmark = new UnlLandmark("Departure", 45.6646, 25.5872);

    // Define the destination.
    UnlLandmark destinationLandmark = new UnlLandmark("Destination", 45.6578, 25.6233);

    // Define the route preferences with public transport mode.
    UnlRoutePreferences routePreferences = new UnlRoutePreferences();
    routePreferences.setTransportMode(EUnlRouteTransportMode.Public);

    UnlRoutingService routingService = new UnlRoutingService(
        routePreferences,
        (routes, errorCode, hint) -> {
            if (errorCode == UnlError.NoError) {
                if (!routes.isEmpty()) {
                    // Get the routes collection from map preferences.
                    RoutesCollection routesMap = mapView != null ? mapView.getPreferences().getRoutes() : null;

                    // Display the routes on map.
                    for (int index = 0; index < routes.size(); index++) {
                        UnlRoute route = routes.get(index);
                        if (routesMap != null) {
                            routesMap.add(route, index == 0, index == 0 ? "Route" : null);
                        }
                    }

                    // Convert normal route to UnlPTRoute
                    UnlPTRoute ptRoute = routes.get(0).toPTRoute();

                    if (ptRoute != null && ptRoute.getSegments() != null) {
                        // Convert each segment to UnlPTRouteSegment
                        for (UnlRouteSegment segment : ptRoute.getSegments()) {
                            UnlPTRouteSegment ptSegment = segment.toPTRouteSegment();
                            if (ptSegment != null) {
                                int transitType = ptSegment.getTransitType();

                                if (ptSegment.isCommon()) { // PT segment
                                    if (ptSegment.getInstructions() != null) {
                                        for (UnlRouteInstruction instruction : ptSegment.getInstructions()) {
                                            UnlPTRouteInstruction ptInstr = instruction.toPTRouteInstruction();
                                            if (ptInstr != null) {
                                                // handle public transit instruction
                                                String stationName = ptInstr.getName();
                                                UnlTime departure = ptInstr.getDepartureTime();
                                                UnlTime arrival = ptInstr.getArrivalTime();
                                                // ...
                                            }
                                        }
                                    }
                                } else { // walk segment
                                    List<UnlRouteInstruction> instructions = ptSegment.getInstructions();
                                    if (instructions != null) {
                                        for (UnlRouteInstruction walkInstr : instructions) {
                                            // handle walk instruction
                                        }
                                    }
                                }
                            }
                        }
                    }
                }
            } else if (errorCode == UnlError.Cancel) {
                // Route computation canceled
            } else {
                // Error occurred
                showDialog("Error: " + UnlError.getMessage(errorCode));
            }
        }
    );

    ArrayList<UnlLandmark> waypoints = new ArrayList<>();
    waypoints.add(departureLandmark);
    waypoints.add(destinationLandmark);
    routingService.calculateRoute(waypoints);
});

```

Once routes are computed, if the computation was for public transport route, you can convert a resulted route to a public transit route via `toPTRoute()`. After that you have full access to the methods specific to this kind of route.

A public transit route is a sequence of one or more segments. Each segment is either a walking segment, either a public transit segment. You can determine the segment type based on the `EUnlTransitType`.

`EUnlTransitType` can have the following values: `Walk`, `Bus`, `Underground`, `Railway`, `Tram`, `WaterTransport`, `Other`, `SharedBike`, `SharedScooter`, `SharedCar`, `Unknown`.

> 💡 **TIP**
>
> Other settings related to public transit (such as departure/arrival time) can be specified within the `UnlRoutePreferences` object passed to the `calculateRoute` method:
>
>* Kotlin
>* Java
>
>```kotlin
>// Kotlin
>val customRoutePreferences = UnlRoutePreferences().apply {
>    transportMode = EUnlRouteTransportMode.Public
>    // The arrival time is set to one hour from now.
>    algorithmType = EUnlPTAlgorithmType.Arrival
>    timestamp = UnlTime(System.currentTimeMillis() + 3600000) // 1 hour from now
>    // Sort the routes by the best time.
>    sortingStrategy = EUnlPTSortingStrategy.BestTime
>    // Accessibility preferences
>    useBikes = false
>    useWheelchair = false
>}
>
>```
>
>```java
> //Java
>UnlRoutePreferences customRoutePreferences = new UnlRoutePreferences();
>customRoutePreferences.setTransportMode(EUnlRouteTransportMode.Public);
>// The arrival time is set to one hour from now.
>customRoutePreferences.setAlgorithmType(EUnlPTAlgorithmType.Arrival);
>customRoutePreferences.setTimestamp(new UnlTime(System.currentTimeMillis() + 3600000)); // 1 hour from now
>// Sort the routes by the best time.
>customRoutePreferences.setSortingStrategy(EUnlPTSortingStrategy.BestTime);
>// Accessibility preferences
>customRoutePreferences.setUseBikes(false);
>customRoutePreferences.setUseWheelchair(false);
>
>```

## Export a Route to file[​](#export-a-route-to-file "Direct link to Export a Route to file")

The `exportToFile` method allows you to export a route from `RouteBookmarks` into a file on disk. This makes it possible to store the route for later use or share it with other systems.

The file will be saved at the exact location provided in the **filePath** parameter, so always ensure the directory exists and is writable.

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    val routeBookmarks = RouteBookmarks.produce("MyRoutes")
    routeBookmarks?.let { bookmarks ->
        //index of the route in this bookmark collection and filepath
        val error = bookmarks.exportToFile(index, filePath)
        when (error) {
            UnlError.NoError -> {
                // Route exported successfully
            }
            UnlError.NotFound -> {
                // The given route index does not exist
            }
            UnlError.Io -> {
                // File cannot be created or written to
            }
            else -> {
                // Other error occurred
            }
        }
    }
}

```

```java
// Java
SdkCall.execute(() -> {
    RouteBookmarks routeBookmarks = RouteBookmarks.produce("MyRoutes");
    if (routeBookmarks != null) {
        //index of the route in this bookmark collection and filepath
        int error = routeBookmarks.exportToFile(index, filePath);
        if (error == UnlError.NoError) {
            // Route exported successfully
        } else if (error == UnlError.NotFound) {
            // The given route index does not exist
        } else if (error == UnlError.Io) {
            // File cannot be created or written to
        } else {
            // Other error occurred
        }
    }
});

```

> 💡 **TIP**
>
> When exporting a route, make sure to handle possible errors:
>
>* **`UnlError.NotFound`** - This occurs if the given route index does not exist.
>* **`UnlError.Io`** - This occurs if the file cannot be created or written to.

## Export a Route as String[​](#export-a-route-as-string "Direct link to Export a Route as String")

The `exportAs` method allows you to export a route into a textual representation. The returned value is a `DataBuffer` containing the full route data in the requested format. This makes it easy to store the route as a file or share it with other applications that support formats like GPX, KML, NMEA, or GeoJSON.

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    val route = routes.first()
    val dataBuffer = route.exportAs(EPathFileFormat.Gpx)
    dataBuffer?.let { buffer ->
        // You now have the full GPX as a DataBuffer
        // Convert to string if needed
        val gpxString = String(buffer.data)
    }
}

```

```java
// Java
SdkCall.execute(() -> {
    UnlRoute route = routes.get(0);
    DataBuffer dataBuffer = route.exportAs(EPathFileFormat.Gpx);
    if (dataBuffer != null) {
        // You now have the full GPX as a DataBuffer
        // Convert to string if needed
        String gpxString = new String(dataBuffer.getData());
    }
});

```
