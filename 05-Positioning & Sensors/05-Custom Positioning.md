# Custom positioning

The Navigation SDK for Android allows setting custom data source with the UnlPositionService to dynamically manage and simulate location data. This approach allows for external or simulated positioning data, providing flexibility beyond traditional GPS signals, and is ideal for testing or custom tracking solutions.

> 💡 **Tip**
>
> Utilizing a custom data source eliminates the need for the previously discussed location permission management.

## Create custom data source[​](#create-custom-data-source "Direct link to Create custom data source")

The following code snippet illustrates how to integrate a custom data source with the UnlPositionService to manage and simulate location data dynamically. Instead of relying on real GPS signals, the custom data source provides flexibility by allowing external or simulated position data to be used in the application.

* Kotlin
* Java

```kotlin
// Kotlin
import com.unlmap.sdk.sensordatasource.*
import com.unlmap.sdk.sensordatasource.enums.EDataType
import java.util.Timer
import kotlin.concurrent.fixedRateTimer

// Create a custom data source
val dataSource = DataSourceFactory.produceExternal(arrayListOf(EDataType.Position))

if (dataSource == null) {
    Log.e(TAG, "The datasource could not be created")
    return
}

// Positions will be provided from the data source
UnlPositionService.dataSource = dataSource

// Start the data source
dataSource.start()

// Push the first position in the data source
val firstPosition = UnlPositionData.produce(
    acquisitionTimestamp = System.currentTimeMillis(),
    latitudeInDegrees = 48.85682,
    longitudeInDegrees = 2.34375,
    altitudeInMeters = 0.0,
    courseInDegrees = 0.0,
    speedInMpS = 0.0
)

firstPosition?.let { dataSource.pushData(it) }

// Optional, usually done if we want the map to take into
// account the current position
mapView.startFollowingPosition()

// Simulate continuous position updates
var timer: Timer? = null
var index = 0
val ticks = 10

timer = fixedRateTimer("positionTimer", false, 0L, 50) {
    // Provide latitude, longitude, heading, speed
    val lat = 45.0
    val lon = 10.0
    val head = 0.0
    val speed = 0.0

    // Add each position to data source
    val position = UnlPositionData.produce(
        acquisitionTimestamp = System.currentTimeMillis(),
        latitudeInDegrees = lat,
        longitudeInDegrees = lon,
        altitudeInMeters = 0.0,
        courseInDegrees = head,
        speedInMpS = speed
    )

    position?.let { dataSource.pushData(it) }

    index++

    if (index >= ticks) {
        timer?.cancel()
    }
}

```

```java
// Java
import com.unlmap.sdk.sensordatasource.*;
import com.unlmap.sdk.sensordatasource.enums.EDataType;
import java.util.Timer;
import java.util.TimerTask;
import java.util.ArrayList;
import java.util.Arrays;

// Create a custom data source
ArrayList<EDataType> dataTypes = new ArrayList<>(Arrays.asList(EDataType.Position));
DataSource dataSource = DataSourceFactory.produceExternal(dataTypes);

if (dataSource == null) {
    Log.e(TAG, "The datasource could not be created");
    return;
}

// Positions will be provided from the data source
UnlPositionService.setDataSource(dataSource);

// Start the data source
dataSource.start();

// Push the first position in the data source
UnlPositionData firstPosition = UnlPositionData.produce(
    System.currentTimeMillis(),
    48.85682,
    2.34375,
    0.0,
    0.0,
    0.0
);

if (firstPosition != null) {
    dataSource.pushData(firstPosition);
}

// Optional, usually done if we want the map to take into
// account the current position
mapView.startFollowingPosition();

// Simulate continuous position updates
Timer timer = new Timer();
final int[] index = {0};
int ticks = 10;

timer.scheduleAtFixedRate(new TimerTask() {
    @Override
    public void run() {
        // Provide latitude, longitude, heading, speed
        double lat = 45.0;
        double lon = 10.0;
        double head = 0.0;
        double speed = 0.0;

        // Add each position to data source
        UnlPositionData position = UnlPositionData.produce(
            System.currentTimeMillis(),
            lat,
            lon,
            0.0,
            head,
            speed
        );

        if (position != null) {
            dataSource.pushData(position);
        }

        index[0]++;

        if (index[0] >= ticks) {
            timer.cancel();
        }
    }
}, 0L, 50L);

```

**How It Works:**

* **Creating and Registering a Custom Data Source**: A custom DataSource object is created and configured to handle position data. This data source is registered with the UnlPositionService, overriding the default GPS-based data provider. This allows the application to retrieve location updates from the custom source.

* **Starting the Data Source**: The custom data source is activated by calling the `start()` method. Once started, it becomes ready to accept and process location data that is pushed into it.

* **Pushing Initial Position Data**: An initial position is sent to the data source using the `pushData()` method. This data includes details such as latitude, longitude, altitude, heading, speed, and a timestamp. It acts as a starting point for tracking the location.

* **Enabling Map Follow Mode**: The `startFollowingPosition()` method ensures the map camera follows the position tracker. As the custom data source provides new position updates, the map view adjusts automatically to keep the position tracker in focus.

* **Updating Location Data in Real-UnlTime**: A timer continuously generates and pushes simulated position updates to the data source at regular intervals (every 50 milliseconds). These updates include coordinates, heading, and speed. This dynamic update mechanism allows the application to simulate movement or integrate location data from custom sources, such as a mock GPS or external tracking systems.

## Improve custom data source positions[​](#improve-custom-data-source-positions "Direct link to Improve custom data source positions")

While providing latitude, longitude, and timestamp may suffice for some use cases, this data may not offer sufficient accuracy, particularly during navigation. In such cases, the system might occasionally register incorrect turns or unexpected deviations. To improve precision, the `courseInDegrees` field of `UnlPositionData` is utilized to indicate the direction of movement, which is factored into the positioning calculations.

### Calculate heading[​](#calculate-heading "Direct link to Calculate heading")

A simple function to calculate the heading knowing the current coordinate and the next coordinate is presented below:

* Kotlin
* Java

```kotlin
// Kotlin
import kotlin.math.*

fun getHeading(from: UnlCoordinates, to: UnlCoordinates): Double {
    val dx = to.longitude - from.longitude
    val dy = to.latitude - from.latitude

    val radianToDegree = 57.2957795

    var value = atan2(dx, dy) * radianToDegree
    if (value < 0) value += 360

    return value
}

```

```java
// Java
public double getHeading(UnlCoordinates from, UnlCoordinates to) {
    double dx = to.getLongitude() - from.getLongitude();
    double dy = to.getLatitude() - from.getLatitude();

    double radianToDegree = 57.2957795;

    double value = Math.atan2(dx, dy) * radianToDegree;
    if (value < 0) value += 360;

    return value;
}

```

### Calculate speed[​](#calculate-speed "Direct link to Calculate speed")

The `speedInMpS` field from the `UnlPositionData` can be computed by dividing the distance between the two coordinates by the duration of the movement between the two coordinates. The distance can be computed using the `distance` method from the `UnlCoordinates` class.

* Kotlin
* Java

```kotlin
// Kotlin
fun getSpeed(from: UnlCoordinates, to: UnlCoordinates, timestampAtFrom: Long, timestampAtTo: Long): Double {
    val timeDiff = (timestampAtTo - timestampAtFrom) / 1000.0 // Convert to seconds
    val distance = from.distance(to)

    return if (timeDiff == 0.0) {
        0.0
    } else {
        distance / timeDiff
    }
}

```

```java
// Java
public double getSpeed(UnlCoordinates from, UnlCoordinates to, long timestampAtFrom, long timestampAtTo) {
    double timeDiff = (timestampAtTo - timestampAtFrom) / 1000.0; // Convert to seconds
    double distance = from.distance(to);

    if (timeDiff == 0.0) {
        return 0.0;
    } else {
        return distance / timeDiff;
    }
}

```

If the coordinates to be pushed in the custom data source are not known, they can be extrapolated based on the previous values.

## Remove the custom datasource[​](#remove-the-custom-datasource "Direct link to Remove the custom datasource")

To remove the data source once we don't need it anymore, we can proceed in the following way:

* Kotlin
* Java

```kotlin
// Kotlin
val dataSource = DataSourceFactory.produceExternal(arrayListOf(EDataType.Position))

if (dataSource == null) {
    Log.e(TAG, "The datasource could not be created")
    return
}

UnlPositionService.dataSource = dataSource
dataSource.start()

// Do something with the data source...

// Stop the data source
dataSource.stop()

// Remove the data source from the position service
UnlPositionService.dataSource = null

```

```java
// Java
ArrayList<EDataType> dataTypes = new ArrayList<>(Arrays.asList(EDataType.Position));
DataSource dataSource = DataSourceFactory.produceExternal(dataTypes);

if (dataSource == null) {
    Log.e(TAG, "The datasource could not be created");
    return;
}

UnlPositionService.setDataSource(dataSource);
dataSource.start();

// Do something with the data source...

// Stop the data source
dataSource.stop();

// Remove the data source from the position service
UnlPositionService.setDataSource(null);

```

> 🚨 **Danger**
>
> It's important to stop the data source and remove it from the position service once work is finished with it. Otherwise there can be unexpected problems especially when trying to use other data sources (live or custom).

## Create a simulation data source[​](#create-a-simulation-data-source "Direct link to Create a simulation data source")

The Navigation SDK allows you to create simulation data source from existing route data. This simulates the device moving along a predefined route.

* Kotlin
* Java

```kotlin
// Kotlin
val route = // ... obtain a route from routing service

val dataSource = DataSourceFactory.produceSimulation(route)

if (dataSource == null) {
    Log.e(TAG, "The simulation datasource could not be created")
    return
}

dataSource.start()

UnlPositionService.dataSource = dataSource

```

```java
// Java
UnlRoute route = // ... obtain a route from routing service

DataSource dataSource = DataSourceFactory.produceSimulation(route);

if (dataSource == null) {
    Log.e(TAG, "The simulation datasource could not be created");
    return;
}

dataSource.start();

UnlPositionService.setDataSource(dataSource);

```

**How It Works:**

* **Creating and Registering a Custom Data Source**: A custom DataSource object is created and configured to handle position data. This data source is registered with the UnlPositionService, overriding the default GPS-based data provider. This allows the application to retrieve location updates from the custom source.

* **Starting the Data Source**: The custom data source is activated by calling the start() method. Once started, it starts simulating the given route.

## Remove the simulation datasource[​](#remove-the-simulation-datasource "Direct link to Remove the simulation datasource")

To remove the simulation data source once we don't need it anymore, we can proceed in the following way:

* Kotlin
* Java

```kotlin
// Kotlin
val route = // ... obtain a route from routing service

val dataSource = DataSourceFactory.produceSimulation(route)

if (dataSource == null) {
    Log.e(TAG, "The simulation datasource could not be created")
    return
}

dataSource.start()
UnlPositionService.dataSource = dataSource

// Do something with the data source...

// Stop the data source
dataSource.stop()

// Remove the data source from the position service
UnlPositionService.dataSource = null

```

```java
// Java
UnlRoute route = // ... obtain a route from routing service

DataSource dataSource = DataSourceFactory.produceSimulation(route);

if (dataSource == null) {
    Log.e(TAG, "The simulation datasource could not be created");
    return;
}

dataSource.start();
UnlPositionService.setDataSource(dataSource);

// Do something with the data source...

// Stop the data source
dataSource.stop();

// Remove the data source from the position service
UnlPositionService.setDataSource(null);

```

> 🚨 **Danger**
>
> It's important to stop the data source and remove it from the position service once work is finished with it. Otherwise there can be unexpected problems especially when trying to use other data sources (live or custom).

## Create log data source[​](#create-log-data-source "Direct link to Create log data source")

The following code snippet illustrates how to integrate a log data source with the UnlPositionService to manage and simulate location data dynamically. Instead of relying on real GPS signals, the log data source provides flexibility by replaying a given log file. This enables the application to replay location data from a stable and predefined log, ensuring uniformity across different runs.

* Kotlin
* Java

```kotlin
// Kotlin
// Create a log data source
val dataSource = DataSourceFactory.produceLog("path/to/logfile.gpx")

if (dataSource == null) {
    Log.e(TAG, "The log datasource could not be created")
    return
}

// Start the data source
dataSource.start()

// Positions will be provided from the data source
UnlPositionService.dataSource = dataSource

// Optionally, you can control playback if the data source supports it
dataSource.playback?.let { playback ->
    playback.speedMultiplier = 2.0f // Play at 2x speed
    playback.resume()
}

```

```java
// Java
// Create a log data source
DataSource dataSource = DataSourceFactory.produceLog("path/to/logfile.gpx");

if (dataSource == null) {
    Log.e(TAG, "The log datasource could not be created");
    return;
}

// Start the data source
dataSource.start();

// Positions will be provided from the data source
UnlPositionService.setDataSource(dataSource);

// Optionally, you can control playback if the data source supports it
Playback playback = dataSource.getPlayback();
if (playback != null) {
    playback.setSpeedMultiplier(2.0f); // Play at 2x speed
    playback.resume();
}

```

> 📝 **Info**
>
> Log data sources need to be explicitly started with `dataSource.start()`. You can also control playback speed and state through the `playback` interface if supported.

**How It Works:**

* **Creating and Registering a Log Data Source**: A log DataSource object is created from a file path and configured to handle position data from the log file. This data source is registered with the UnlPositionService, overriding the default GPS-based data provider.

* **Automatic Playback**: The log data source automatically starts playing back the recorded data once it's created and registered, providing consistent position updates based on the log file content.

## Remove the log datasource[​](#remove-the-log-datasource "Direct link to Remove the log datasource")

To remove the data source once we don't need it anymore, we can proceed in the following way:

* Kotlin
* Java

```kotlin
// Kotlin
val dataSource = DataSourceFactory.produceLog("path/to/logfile.gpx")

if (dataSource == null) {
    Log.e(TAG, "The log datasource could not be created")
    return
}

UnlPositionService.dataSource = dataSource

// Do something with the data source...

// Stop the data source
dataSource.stop()

// Remove the data source from the position service
UnlPositionService.dataSource = null

```

```java
// Java
DataSource dataSource = DataSourceFactory.produceLog("path/to/logfile.gpx");

if (dataSource == null) {
    Log.e(TAG, "The log datasource could not be created");
    return;
}

UnlPositionService.setDataSource(dataSource);

// Do something with the data source...

// Stop the data source
dataSource.stop();

// Remove the data source from the position service
UnlPositionService.setDataSource(null);

```

> 🚨 **Danger**
>
> The log data source cannot be of type `.gm`. Using this file type is not supported in the public SDK. You can use another format, such as `gpx`, `nmea` or `kml`, that can be exported from the `.gm` file.

> 🚨 **Danger**
>
> It's important to stop the data source and remove it from the position service once work is finished with it. Otherwise there can be unexpected problems especially when trying to use other data sources (live or custom).
