# Areas alarms

Another powerful use case is triggering operations the moment a user enters or exits a defined **geographic area**.

The UNL Navigation SDK for Android includes a built-in `UnlAlarmService` class, making it effortless to configure and manage all your geofence events.

## Add areas to be monitored[​](#add-areas-to-be-monitored "Direct link to Add areas to be monitored")

Define your geographic areas-`RectangleGeographicArea`, `CircleGeographicArea`, and `UnlPolygonGeographicArea`-then invoke the `monitorArea` method on your `UnlAlarmService` instance:

* Kotlin
* Java

```kotlin
// Kotlin
// Create a rectangular area using minLat, maxLat, minLon, maxLon
val rect = RectangleGeographicArea(
    minLat = 0.5,   // minimum latitude
    maxLat = 1.0,   // maximum latitude
    minLon = 0.5,   // minimum longitude
    maxLon = 1.0    // maximum longitude
)

// Create a circular area with center coordinates and radius in meters
val circle = CircleGeographicArea(
    center = UnlCoordinates(latitude = 1.0, longitude = 0.5),
    radius = 100 // radius in meters
)

// Create a polygon area with a list of coordinates (first and last should be identical to close the polygon)
val polygonCoordinates = arrayListOf(
    UnlCoordinates(latitude = 1.0, longitude = 0.5),
    UnlCoordinates(latitude = 0.5, longitude = 1.0),
    UnlCoordinates(latitude = 1.0, longitude = 1.0),
    UnlCoordinates(latitude = 1.0, longitude = 0.5) // Close the polygon
)
val polygon = UnlPolygonGeographicArea(polygonCoordinates)

// Monitor the areas
SdkCall.execute {
    alarmService?.monitorArea(rect)
    alarmService?.monitorArea(circle)
    alarmService?.monitorArea(polygon)
}

```

```java
// Java
// Create a rectangular area using minLat, maxLat, minLon, maxLon
RectangleGeographicArea rect = new RectangleGeographicArea(
    0.5,   // minimum latitude
    1.0,   // maximum latitude
    0.5,   // minimum longitude
    1.0    // maximum longitude
);

// Create a circular area with center coordinates and radius in meters
CircleGeographicArea circle = new CircleGeographicArea(
    new UnlCoordinates(1.0, 0.5),
    100 // radius in meters
);

// Create a polygon area with a list of coordinates (first and last should be identical to close the polygon)
ArrayList<UnlCoordinates> polygonCoordinates = new ArrayList<>();
polygonCoordinates.add(new UnlCoordinates(1.0, 0.5));
polygonCoordinates.add(new UnlCoordinates(0.5, 1.0));
polygonCoordinates.add(new UnlCoordinates(1.0, 1.0));
polygonCoordinates.add(new UnlCoordinates(1.0, 0.5)); // Close the polygon
UnlPolygonGeographicArea polygon = new UnlPolygonGeographicArea(polygonCoordinates);

// Monitor the areas
SdkCall.execute(() -> {
    if (alarmService != null) {
        alarmService.monitorArea(rect);
        alarmService.monitorArea(circle);
        alarmService.monitorArea(polygon);
    }
});

```

The SDK automatically manages area identification internally for boundary crossing detection.

## Get a list of monitored areas[​](#get-a-list-of-monitored-areas "Direct link to Get a list of monitored areas")

Access your active geofences via the `monitoredAreas` property, which returns a list of `AlarmMonitoredArea` objects, each one reflecting the parameters you provided to `monitorArea`.

* Kotlin
* Java

```kotlin
// Kotlin
val monitoredAreas = alarmService?.monitoredAreas ?: emptyList()

for (monitoredArea in monitoredAreas) {
    val area = monitoredArea.area
    val id = monitoredArea.id
    Log.d("AreaMonitoring", "Monitoring area: $id")
}

```

```java
// Java
List<AlarmMonitoredArea> monitoredAreas = alarmService != null ? alarmService.getMonitoredAreas() : Collections.emptyList();

for (AlarmMonitoredArea monitoredArea : monitoredAreas) {
    UnlGeographicArea area = monitoredArea.getArea();
    int id = monitoredArea.getId();
    Log.d("AreaMonitoring", "Monitoring area: " + id);
}

```

> 💡 **TIP**
>
> When defining a `UnlPolygonGeographicArea`, always "close" the shape by making the first and last coordinates identical. Otherwise, the SDK may return polygons that don't match the one you provided.

## Unmonitor an area[​](#unmonitor-an-area "Direct link to Unmonitor an area")

To remove a monitored area, call the `unmonitorArea` method and pass in the same `UnlGeographicArea` instance you originally supplied to `monitorArea`. This will unregister that zone and stop all related geofence events.

* Kotlin
* Java

```kotlin
// Kotlin
val rect = RectangleGeographicArea(
    minLat = 0.5,
    maxLat = 1.0,
    minLon = 0.5,
    maxLon = 1.0
)

alarmService?.monitorArea(rect)
// Later, unmonitor the same area
alarmService?.unmonitorArea(rect)

```

```java
// Java
RectangleGeographicArea rect = new RectangleGeographicArea(
    0.5,
    1.0,
    0.5,
    1.0
);

if (alarmService != null) {
    alarmService.monitorArea(rect);
    // Later, unmonitor the same area
    alarmService.unmonitorArea(rect);
}

```

To unmonitor multiple areas, call `unmonitorArea` for each area individually.

## Get notified when the user enters an area:[​](#get-notified-when-the-user-enters-an-area "Direct link to Get notified when the user enters an area:")

Attach your `UnlAlarmListener`-including the `onBoundaryCrossed` callback - to your `UnlAlarmService`. This callback is triggered when the user crosses area boundaries.

* Kotlin
* Java

```kotlin
// Kotlin
val alarmListener = UnlAlarmListener.create {
    onBoundaryCrossed {
        Log.d("BoundaryCrossed", "Boundary crossed detected")

        // Handle area entry/exit events
        // Update UI based on area changes
        // Use alarmService?.crossedBoundaries to get detailed boundary information
    }
}

val alarmService = UnlAlarmService.produce(alarmListener)

```

```java
// Java
UnlAlarmListener alarmListener = UnlAlarmListener.create(
    () -> {
        Log.d("BoundaryCrossed", "Boundary crossed detected");

        // Handle area entry/exit events
        // Update UI based on area changes
        // Use alarmService.getCrossedBoundaries() to get detailed boundary information
    }
);

UnlAlarmService alarmService = UnlAlarmService.produce(alarmListener);

```

## Get the list of areas where the user is located[​](#get-the-list-of-areas-where-the-user-is-located "Direct link to Get the list of areas where the user is located")

Retrieve the zones the user is currently inside by calling the `insideAreas` property on your `UnlAlarmService` instance:

* Kotlin
* Java

```kotlin
// Kotlin
val insideAreas = alarmService?.insideAreas ?: emptyList()
Log.d("AreaMonitoring", "User is inside ${insideAreas.size} areas")

```

```java
// Java
List<UnlGeographicArea> insideAreas = alarmService != null ? alarmService.getInsideAreas() : Collections.emptyList();
Log.d("AreaMonitoring", "User is inside " + insideAreas.size() + " areas");

```

> 📝 **INFO**
>
> For the insideAreas property to return a non-empty list, the user must be inside at least one monitored area and must move or change position within that area.

To retrieve the zones the user has exited, call the `outsideAreas` property on your `UnlAlarmService` instance.

Here's a complete Android Activity example that demonstrates area alarm functionality:

* Kotlin
* Java

```kotlin
// Kotlin
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import com.unlmap.sdk.core.*
import com.unlmap.sdk.places.UnlCoordinates

class AreaAlarmsActivity : AppCompatActivity() {

    private var alarmService: UnlAlarmService? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setupAreaAlarms()
        createAndMonitorAreas()
    }

    private fun setupAreaAlarms() {
        // Create alarm listener with boundary crossing callback
        val alarmListener = UnlAlarmListener.create {
            onBoundaryCrossed {
                Log.d("AreaAlarm", "Boundary crossed detected")

                // Update UI to show area status changes
                // Check crossedBoundaries for detailed information
                SdkCall.execute {
                    val crossedBoundaries = alarmService?.crossedBoundaries
                    Log.i("AreaAlarm", "Crossed boundaries: $crossedBoundaries")
                }
            }
        }

        // Create alarm service
        alarmService = UnlAlarmService.produce(alarmListener)
    }

    private fun createAndMonitorAreas() {
            // Create different types of geographic areas
            val rectangularArea = RectangleGeographicArea(
                minLat = 37.7649,
                maxLat = 37.7849,
                minLon = -122.4294,
                maxLon = -122.4094
            )

            val circularArea = CircleGeographicArea(
                center = UnlCoordinates(latitude = 37.7749, longitude = -122.4194),
                radius = 500 // 500 meters radius
            )

            val polygonCoordinates = arrayListOf(
                UnlCoordinates(latitude = 37.7749, longitude = -122.4194),
                UnlCoordinates(latitude = 37.7799, longitude = -122.4144),
                UnlCoordinates(latitude = 37.7849, longitude = -122.4194),
                UnlCoordinates(latitude = 37.7799, longitude = -122.4244),
               UnlCoordinates(latitude = 37.7749, longitude = -122.4194) // Close the polygon
            )
            val polygonArea = UnlPolygonGeographicArea(polygonCoordinates)

            // Monitor all areas

            alarmService?.monitorArea(rectangularArea)
            alarmService?.monitorArea(circularArea)
            alarmService?.monitorArea(polygonArea)

            Log.d("AreaAlarm", "Started monitoring 3 geographic areas")
            checkMonitoredAreas()
        }
    }

    private fun checkMonitoredAreas() {
        SdkCall.execute {
            val monitoredAreas = alarmService?.monitoredAreas ?: emptyList()
            Log.d("AreaAlarm", "Currently monitoring ${monitoredAreas.size} areas")

            for (area in monitoredAreas) {
                Log.d("AreaAlarm", "Area ID: ${area.id}")
            }

            // Check which areas user is currently inside
            val insideAreas = alarmService?.insideAreas ?: emptyList()
            Log.d("AreaAlarm", "User is inside ${insideAreas.size} areas")
        }
    }

    override fun onDestroy() {
        super.onDestroy()

        // Clean up - unmonitor all areas
        SdkCall.execute {
            // Unmonitor areas individually using the original area instances
            alarmService?.unmonitorArea(rectangularArea)
            alarmService?.unmonitorArea(circularArea)
            alarmService?.unmonitorArea(polygonArea)
            Log.d("AreaAlarm", "Unmonitored all areas")
        }
    }
}

```

```java
// Java
import android.os.Bundle;
import android.util.Log;
import androidx.appcompat.app.AppCompatActivity;
import com.unlmap.sdk.core.*;
import com.unlmap.sdk.places.UnlCoordinates;

class AreaAlarmsActivity extends AppCompatActivity {

    private UnlAlarmService alarmService = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setupAreaAlarms();
        createAndMonitorAreas();
    }

    private void setupAreaAlarms() {
        // Create alarm listener with boundary crossing callback
        UnlAlarmListener alarmListener = UnlAlarmListener.create(
            () -> {
                Log.d("AreaAlarm", "Boundary crossed detected");

                // Update UI to show area status changes
                // Check crossedBoundaries for detailed information
                SdkCall.execute(() -> {
                    Object crossedBoundaries = alarmService != null ? alarmService.getCrossedBoundaries() : null;
                    Log.i("AreaAlarm", "Crossed boundaries: " + crossedBoundaries);
                });
            }
        );

        // Create alarm service
        alarmService = UnlAlarmService.produce(alarmListener);
    }

    private void createAndMonitorAreas() {
            // Create different types of geographic areas
            RectangleGeographicArea rectangularArea = new RectangleGeographicArea(
                37.7649,
                37.7849,
                -122.4294,
                -122.4094
            );

            CircleGeographicArea circularArea = new CircleGeographicArea(
                new UnlCoordinates(37.7749, -122.4194),
                500 // 500 meters radius
            );

            ArrayList<UnlCoordinates> polygonCoordinates = new ArrayList<>();
            polygonCoordinates.add(new UnlCoordinates(37.7749, -122.4194));
            polygonCoordinates.add(new UnlCoordinates(37.7799, -122.4144));
            polygonCoordinates.add(new UnlCoordinates(37.7849, -122.4194));
            polygonCoordinates.add(new UnlCoordinates(37.7799, -122.4244));
            polygonCoordinates.add(new UnlCoordinates(37.7749, -122.4194)); // Close the polygon
            UnlPolygonGeographicArea polygonArea = new UnlPolygonGeographicArea(polygonCoordinates);

            // Monitor all areas
            if (alarmService != null) {
                alarmService.monitorArea(rectangularArea);
                alarmService.monitorArea(circularArea);
                alarmService.monitorArea(polygonArea);
            }

            Log.d("AreaAlarm", "Started monitoring 3 geographic areas");
            checkMonitoredAreas();
        }
    }

    private void checkMonitoredAreas() {
        SdkCall.execute(() -> {
            List<AlarmMonitoredArea> monitoredAreas = alarmService != null ? alarmService.getMonitoredAreas() : Collections.emptyList();
            Log.d("AreaAlarm", "Currently monitoring " + monitoredAreas.size() + " areas");

            for (AlarmMonitoredArea area : monitoredAreas) {
                Log.d("AreaAlarm", "Area ID: " + area.getId());
            }

            // Check which areas user is currently inside
            List<UnlGeographicArea> insideAreas = alarmService != null ? alarmService.getInsideAreas() : Collections.emptyList();
            Log.d("AreaAlarm", "User is inside " + insideAreas.size() + " areas");
        });
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();

        // Clean up - unmonitor all areas
        SdkCall.execute(() -> {
            if (alarmService != null) {
                // Unmonitor areas individually using the original area instances
                alarmService.unmonitorArea(rectangularArea);
                alarmService.unmonitorArea(circularArea);
                alarmService.unmonitorArea(polygonArea);
            }
            Log.d("AreaAlarm", "Unmonitored all areas");
        });
    }
}

```

This example demonstrates:

* Creating different types of geographic areas (rectangle, circle, polygon)
* Monitoring areas with the UnlAlarmService
* Handling boundary crossing events with proper Android logging
* Checking currently monitored and inside areas
* Proper cleanup when the activity is destroyed
