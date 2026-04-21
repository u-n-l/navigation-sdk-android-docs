# Get started with positioning

The Positioning module enables your application to obtain and utilize location data, serving as the foundation for features like navigation, tracking, and location-based services. This data can be sourced either from the device's GPS or from a custom data source, offering flexibility to suit diverse application needs.

Using the Positioning module, you can:

* Leverage real GPS data: Obtain highly accurate, real-time location updates directly from the device's built-in GPS sensor.
* Integrate custom location data: Configure the module to use location data provided by external sources, such as mock services or specialized hardware.

In the following sections, you will learn how to grant the necessary location permissions for your app, set up live data sources, and manage location updates effectively. Additionally, we will explore how to customize the position tracker to align with your application's design and functionality requirements. This comprehensive guide will help you integrate robust and flexible positioning capabilities into your app.

## Granting permissions[​](#granting-permissions "Direct link to Granting permissions")

### Add application location permissions[​](#add-application-location-permissions "Direct link to Add application location permissions")

Location permissions must be configured in your Android application. In order to give the application the location permission on Android, you need to alter the `app/src/main/AndroidManifest.xml` and add the following permissions within the `<manifest>` block:

```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION" />

```

More information about permissions can be found in the [Android Manifest documentation](https://developer.android.com/reference/android/Manifest.permission).

### Get user consent[​](#get-user-consent "Direct link to Get user consent")

Defining location permissions in the application, as explained in the previous section, is a mandatory step.

* Kotlin
* Java

```kotlin
// Kotlin
// Check if location permissions are granted
if (ContextCompat.checkSelfPermission(
        this,
        Manifest.permission.ACCESS_FINE_LOCATION
    ) == PackageManager.PERMISSION_GRANTED
) {
    // After the permission was granted, we can set the live data source (in most cases the GPS).
    // The data source should be set only once, otherwise we'll get an error.
    SdkCall.execute {
        val liveDataSource = DataSourceFactory.produceLive()
        liveDataSource?.let {
            UnlPositionService.dataSource = it
            Log.d(TAG, "Live data source set successfully")
        } ?: run {
            Log.e(TAG, "Failed to create live data source")
        }
    }
} else {
    // Request location permission
    ActivityCompat.requestPermissions(
        this,
        arrayOf(
            Manifest.permission.ACCESS_FINE_LOCATION,
            Manifest.permission.ACCESS_COARSE_LOCATION
        ),
        LOCATION_PERMISSION_REQUEST_CODE
    )
}

```

```java
// Java
// Check if location permissions are granted
if (ContextCompat.checkSelfPermission(
        this,
        Manifest.permission.ACCESS_FINE_LOCATION
    ) == PackageManager.PERMISSION_GRANTED
) {
    // After the permission was granted, we can set the live data source (in most cases the GPS).
    // The data source should be set only once, otherwise we'll get an error.
    SdkCall.execute(() -> {
        DataSource liveDataSource = DataSourceFactory.produceLive();
        if (liveDataSource != null) {
            UnlPositionService.setDataSource(liveDataSource);
            Log.d(TAG, "Live data source set successfully");
        } else {
            Log.e(TAG, "Failed to create live data source");
        }
    });
} else {
    // Request location permission
    ActivityCompat.requestPermissions(
        this,
        new String[]{
            Manifest.permission.ACCESS_FINE_LOCATION,
            Manifest.permission.ACCESS_COARSE_LOCATION
        },
        LOCATION_PERMISSION_REQUEST_CODE
    );
}

```

Handle the permission request result in your activity:

* Kotlin
* Java

```kotlin
// Kotlin
override fun onRequestPermissionsResult(
    requestCode: Int,
    permissions: Array<out String>,
    grantResults: IntArray
) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults)

    when (requestCode) {
        LOCATION_PERMISSION_REQUEST_CODE -> {
            if (grantResults.isNotEmpty() && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                // Permission granted, set up live data source
                SdkCall.execute {
                    val liveDataSource = DataSourceFactory.produceLive()
                    liveDataSource?.let {
                        UnlPositionService.dataSource = it
                        Log.d(TAG, "Live data source set successfully")
                    }
                }
            } else {
                // Permission denied
                Log.w(TAG, "Location permission denied")
            }
        }
    }
}

```

```java
// Java
@Override
public void onRequestPermissionsResult(
    int requestCode,
    String[] permissions,
    int[] grantResults
) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);

    switch (requestCode) {
        case LOCATION_PERMISSION_REQUEST_CODE:
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                // Permission granted, set up live data source
                SdkCall.execute(() -> {
                    DataSource liveDataSource = DataSourceFactory.produceLive();
                    if (liveDataSource != null) {
                        UnlPositionService.setDataSource(liveDataSource);
                        Log.d(TAG, "Live data source set successfully");
                    }
                });
            } else {
                // Permission denied
                Log.w(TAG, "Location permission denied");
            }
            break;
    }
}

```

In the previous code, we request the location permissions. If the user grants them, we notify the engine to use real GPS positions for navigation by creating a live data source and setting it on the `UnlPositionService`.

Alternatively, you can use `PermissionHelper` from the SDK utility package to simplify permission handling:

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    val hasLocationPermission =
        PermissionsHelper.hasPermission(this, Manifest.permission.ACCESS_FINE_LOCATION)
    if (hasLocationPermission)
        SdkCall.execute {
            val liveDataSource = DataSourceFactory.produceLive()
            liveDataSource?.let {
                UnlPositionService.dataSource = it
                Log.d(TAG, "Live data source set successfully")
            }
        }
    else
        requestPermissions(this)
}

```

```java
// Java
SdkCall.execute(() -> {
    boolean hasLocationPermission =
        PermissionsHelper.hasPermission(this, Manifest.permission.ACCESS_FINE_LOCATION);
    if (hasLocationPermission) {
        SdkCall.execute(() -> {
            DataSource liveDataSource = DataSourceFactory.produceLive();
            if (liveDataSource != null) {
                UnlPositionService.setDataSource(liveDataSource);
                Log.d(TAG, "Live data source set successfully");
            }
        });
    } else {
        requestPermissions(this);
    }
});

```

If the live data source is set and the right permissions are granted, the position cursor should be visible on the map as an arrow.

> 💡 **Tip**
>
> For debugging purposes, the Android Emulator's [extended controls](https://developer.android.com/studio/run/emulator-extended-controls) can be used to mock the current position. On a real device, apps such as [Mock Locations](https://play.google.com/store/apps/details?id=ru.gavrikov.mocklocations) can be utilized to simulate location data.

Don't forget to add the necessary import statements and member declarations to your activity:

* Kotlin
* Java

```kotlin
// Kotlin
import android.Manifest
import android.content.pm.PackageManager
import android.util.Log
import androidx.core.app.ActivityCompat
import androidx.core.content.ContextCompat
import com.unlmap.sdk.sensordatasource.DataSourceFactory
import com.unlmap.sdk.sensordatasource.UnlPositionService
import com.unlmap.sdk.util.SdkCall

class YourActivity : AppCompatActivity() {
    // ...
    companion object {
        private const val LOCATION_PERMISSION_REQUEST_CODE = 1001
        private const val TAG = "PositioningExample"
    }
}

```

```java
// Java
import android.Manifest;
import android.content.pm.PackageManager;
import android.util.Log;
import androidx.core.app.ActivityCompat;
import androidx.core.content.ContextCompat;
import com.unlmap.sdk.sensordatasource.DataSourceFactory;
import com.unlmap.sdk.sensordatasource.UnlPositionService;
import com.unlmap.sdk.util.SdkCall;

public class YourActivity extends AppCompatActivity {
    // ...
    private static final int LOCATION_PERMISSION_REQUEST_CODE = 1001;
    private static final String TAG = "PositioningExample";
}

```

## Receive location updates[​](#receive-location-updates "Direct link to Receive location updates")

To receive updates about changes in the current position, we can register a callback function using the `UnlPositionListener`. The given listener will be called continuously as new updates about current position are made.

> 💡 **Tip**
>
> Consult the [Positions guide](../03-Core/03-Positions.md) for more information about the `UnlPositionData` class and the differences between raw positions and map matched position.

### Raw positions[​](#raw-positions "Direct link to Raw positions")

To listen for raw position updates (as they are pushed to the data source or received via the sensors), you can add a `UnlPositionListener` for the `EDataType.Position`:

* Kotlin
* Java

```kotlin
// Kotlin
val positionListener = UnlPositionListener { position: UnlPositionData ->
    // Process the position
    if (position.isValid()) {
        val coordinates = position.coordinates
        println("New raw position: $coordinates")
    }
}

// Add the listener
UnlPositionService.addListener(positionListener, EDataType.Position)

```

```java
// Java
UnlPositionListener positionListener = (UnlPositionData position) -> {
    // Process the position
    if (position.isValid()) {
        UnlCoordinates coordinates = position.getCoordinates();
        System.out.println("New raw position: " + coordinates);
    }
};

// Add the listener
UnlPositionService.addListener(positionListener, EDataType.Position);

```

### Map matched positions[​](#map-matched-positions "Direct link to Map matched positions")

To register a callback to receive map matched positions, use `EDataType.ImprovedPosition`:

* Kotlin
* Java

```kotlin
// Kotlin
val improvedPositionListener = UnlPositionListener { position: UnlPositionData ->
    if (position.isValid() && position is ImprovedPositionData) {
        // Current coordinates
        val coordinates = position.coordinates
        println("New improved position: $coordinates")

        // Speed in m/s (-1 if not available)
        val speed = position.speed
        println("Speed: $speed m/s")

        // Speed limit in m/s on the current road (0 if not available)
        val speedLimit = position.roadSpeedLimit
        println("Speed limit: $speedLimit m/s")

        // Heading angle in degrees (N=0, E=90, S=180, W=270, -1 if not available)
        val course = position.course
        println("Course: $course degrees")

        // Information about current road (if it is in a tunnel, bridge, ramp, one way, etc.)
        val roadModifiers = position.roadModifier
        println("Road modifiers: $roadModifiers")

        // Quality of the current position
        val fixQuality = position.fixQuality
        println("Fix quality: $fixQuality")

        // Horizontal and vertical accuracy in meters
        val horizontalAccuracy = position.horizontalAccuracy
        val verticalAccuracy = position.verticalAccuracy
        println("Accuracy - H: $horizontalAccuracy, V: $verticalAccuracy")

        // Road address information (if available)
        if (position.hasRoadLocalization()) {
            val roadAddress = position.roadAddress
            println("Road address: $roadAddress")
        }
    }
}

// Add the listener
UnlPositionService.addListener(improvedPositionListener, EDataType.ImprovedPosition)

```

```java
// Java
UnlPositionListener improvedPositionListener = (UnlPositionData position) -> {
    if (position.isValid() && position instanceof ImprovedPositionData) {
        ImprovedPositionData improvedPosition = (ImprovedPositionData) position;
        // Current coordinates
        UnlCoordinates coordinates = improvedPosition.getCoordinates();
        System.out.println("New improved position: " + coordinates);

        // Speed in m/s (-1 if not available)
        double speed = improvedPosition.getSpeed();
        System.out.println("Speed: " + speed + " m/s");

        // Speed limit in m/s on the current road (0 if not available)
        double speedLimit = improvedPosition.getRoadSpeedLimit();
        System.out.println("Speed limit: " + speedLimit + " m/s");

        // Heading angle in degrees (N=0, E=90, S=180, W=270, -1 if not available)
        double course = improvedPosition.getCourse();
        System.out.println("Course: " + course + " degrees");

        // Information about current road (if it is in a tunnel, bridge, ramp, one way, etc.)
        int roadModifiers = improvedPosition.getRoadModifier();
        System.out.println("Road modifiers: " + roadModifiers);

        // Quality of the current position
        int fixQuality = improvedPosition.getFixQuality();
        System.out.println("Fix quality: " + fixQuality);

        // Horizontal and vertical accuracy in meters
        double horizontalAccuracy = improvedPosition.getHorizontalAccuracy();
        double verticalAccuracy = improvedPosition.getVerticalAccuracy();
        System.out.println("Accuracy - H: " + horizontalAccuracy + ", V: " + verticalAccuracy);

        // Road address information (if available)
        if (improvedPosition.hasRoadLocalization()) {
            String roadAddress = improvedPosition.getRoadAddress();
            System.out.println("Road address: " + roadAddress);
        }
    }
};

// Add the listener
UnlPositionService.addListener(improvedPositionListener, EDataType.ImprovedPosition);

```

Remember to remove listeners when they are no longer needed:

* Kotlin
* Java

```kotlin
// Kotlin
// Remove specific listener
UnlPositionService.removeListener(positionListener)
UnlPositionService.removeListener(improvedPositionListener)

```

```java
// Java
// Remove specific listener
UnlPositionService.removeListener(positionListener);
UnlPositionService.removeListener(improvedPositionListener);

```

> 📝 **Info**
>
> During simulation, the positions provided through the `UnlPositionListener` methods correspond to the simulated locations generated as part of the navigation simulation process.

## Get current location[​](#get-current-location "Direct link to Get current location")

To retrieve the current location, we can use the `position` and `improvedPosition` properties from the `UnlPositionService` object. These properties return a `UnlPositionData` object containing the latest location information or `null` if no position data is available. This method is useful for accessing the most recent position data without registering for continuous updates.

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    val position = UnlPositionService.position

    if (position == null) {
        Log.w(TAG, "No position available")
    } else {
        Log.d(TAG, "Position: ${position.coordinates}")
    }
}

```

```java
// Java
SdkCall.execute(() -> {
    UnlPositionData position = UnlPositionService.getPosition();

    if (position == null) {
        Log.w(TAG, "No position available");
    } else {
        Log.d(TAG, "Position: " + position.getCoordinates());
    }
});

```

For map-matched positions, use the `improvedPosition` property:

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    val improvedPosition = UnlPositionService.improvedPosition

    if (improvedPosition == null) {
        Log.w(TAG, "No improved position available")
    } else {
        Log.d(TAG, "Improved position: ${improvedPosition.coordinates}")

        // Access additional improved position data
        if (improvedPosition is ImprovedPositionData) {
            if (improvedPosition.hasRoadLocalization()) {
                val roadAddress = improvedPosition.roadAddress
                println("Current road: $roadAddress")
            }

            val speedLimit = improvedPosition.roadSpeedLimit
            println("Speed limit: $speedLimit m/s")
        }
    }
}

```

```java
// Java
SdkCall.execute(() -> {
    UnlPositionData improvedPosition = UnlPositionService.getImprovedPosition();

    if (improvedPosition == null) {
        Log.w(TAG, "No improved position available");
    } else {
        Log.d(TAG, "Improved position: " + improvedPosition.getCoordinates());

        // Access additional improved position data
        if (improvedPosition instanceof ImprovedPositionData) {
            ImprovedPositionData improved = (ImprovedPositionData) improvedPosition;
            if (improved.hasRoadLocalization()) {
                String roadAddress = improved.getRoadAddress();
                System.out.println("Current road: " + roadAddress);
            }

            double speedLimit = improved.getRoadSpeedLimit();
            System.out.println("Speed limit: " + speedLimit + " m/s");
        }
    }
});

```

You can also use the convenience methods to get coordinates directly:

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    val currentCoords = UnlPositionService.getCurrentPosition()
    val improvedCoords = UnlPositionService.getCurrentImprovedPosition()

    currentCoords?.let {
        println("Current coordinates: $it")
    }

    improvedCoords?.let {
        println("Improved coordinates: $it")
    }
}

```

```java
// Java
SdkCall.execute(() -> {
    UnlCoordinates currentCoords = UnlPositionService.getCurrentPosition();
    UnlCoordinates improvedCoords = UnlPositionService.getCurrentImprovedPosition();

    if (currentCoords != null) {
        System.out.println("Current coordinates: " + currentCoords);
    }

    if (improvedCoords != null) {
        System.out.println("Improved coordinates: " + improvedCoords);
    }
});

```
