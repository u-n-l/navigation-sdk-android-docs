# Landmark and overlay alarms

The UnlAlarmService can be configured to send notifications upon approaching specific landmarks or overlay items within a defined proximity. This behavior can be tailored to trigger notifications exclusively during navigation or simulation modes, or while freely exploring the map without a predefined route.

This can be used to implement different use cases such as:

* Notify users about incoming reports such as speed cameras, police, accidents or other road hazards.
* Notify users when approaching points of interest, such as historical landmarks, monuments, or scenic viewpoints.
* Notify users about traffic signs such as stop and give way signs.

> 💡 **TIP**
>
> You can search for landmarks along the active route, whether in navigation or simulation mode, using specific categories or other criteria. Once identified, these landmarks can be added for monitoring. Be sure to account for potential route deviations.

> 🚨 **DANGER**
>
> If notifications are not sent via the `UnlAlarmListener` make sure that:
>
>* The `UnlAlarmService` and `UnlAlarmListener` are properly initialized and are kept alive
>* The `alarmDistance` and `monitorWithoutRoute` properties are configured as needed
>* The stores / overlays to be monitored are **successfully** added to the `UnlAlarmService`
>* The overlay items are on the correct side of the road. If the items are on the opposite side of the road, notifications will not be triggered

## Configure the alarm distance[​](#configure-the-alarm-distance "Direct link to Configure the alarm distance")

The distance threshold measured in meters for triggering notifications when approaching a landmark or overlay item can be obtained as follows:

* Kotlin
* Java

```kotlin
// Kotlin
val alarmDistance = alarmService?.alarmDistance ?: 0.0

```

```java
// Java
double alarmDistance = alarmService != null ? alarmService.getAlarmDistance() : 0.0;

```

This threshold can also be adjusted as needed. For example, the following snippet will configure the alarm service to start sending notifications when within 200 meters of one of the monitored items:

* Kotlin
* Java

```kotlin
// Kotlin
alarmService?.alarmDistance = 200.0

```

```java
// Java
if (alarmService != null) {
    alarmService.setAlarmDistance(200.0);
}

```

## Configure alarms without active navigation[​](#configure-alarms-without-active-navigation "Direct link to Configure alarms without active navigation")

The UnlAlarmService can be configured to control whether notifications about approaching landmarks or overlay items are triggered exclusively during navigation (or navigation simulation) on a predefined route, or if they should also occur while freely exploring the map without an active navigation session.

The following snippet demonstrates how to enable notifications for monitored landmarks at all times, regardless of whether navigation is active:

* Kotlin
* Java

```kotlin
// Kotlin
alarmService?.monitorWithoutRoute = true

```

```java
// Java
if (alarmService != null) {
    alarmService.setMonitorWithoutRoute(true);
}

```

The value of the preference set above can be accessed as follows:

* Kotlin
* Java

```kotlin
// Kotlin
val isMonitoringWithoutRoute = alarmService?.monitorWithoutRoute ?: false

```

```java
// Java
boolean isMonitoringWithoutRoute = alarmService != null ? alarmService.getMonitorWithoutRoute() : false;

```

## Landmark alarms[​](#landmark-alarms "Direct link to Landmark alarms")

### Configure alarms listeners[​](#configure-alarms-listeners "Direct link to Configure alarms listeners")

Users are notified through the `onLandmarkAlarmsUpdated` and `onLandmarkAlarmsPassedOver` callbacks when approaching the specified landmarks and when the landmarks have been passed respectively.

The following snippet demonstrates how to retrieve the next landmark to be intercepted and the distance to it and how to detect when a landmark has been passed:

* Kotlin
* Java

```kotlin
// Kotlin
val alarmListener = UnlAlarmListener.create(
    onLandmarkAlarmsUpdated = {
        SdkCall.execute {
            // The landmark alarm list containing the landmarks that are to be intercepted
            val landmarkAlarms = alarmService?.landmarkAlarms

            if (landmarkAlarms != null && landmarkAlarms.size > 0) {
                // Get the closest landmark and its associated distance (in meters)
                val closestLandmark = landmarkAlarms.getItem(0)
                val distance = landmarkAlarms.getDistance(0)

                closestLandmark?.let { landmark ->
                    Log.i("LandmarkAlarm", "The landmark ${landmark.name} is ${distance.toInt()} meters away")
                }
            }
        }
    },
    onLandmarkAlarmsPassedOver = {
        Log.d("LandmarkAlarm", "UnlLandmark was passed over")

        SdkCall.execute {
            // The landmarks that were passed over
            val landmarkAlarmsPassedOver = alarmService?.landmarkAlarmsPassedOver

            // Process the landmarks that were passed over
            landmarkAlarmsPassedOver?.let { passedAlarms ->
                for (i in 0 until passedAlarms.size) {
                    val landmark = passedAlarms.getItem(i)
                    landmark?.let {
                        Log.d("LandmarkAlarm", "Passed landmark: ${it.name}")
                    }
                }
            }
        }
    }
)

```

```java
// Java
UnlAlarmListener alarmListener = UnlAlarmListener.create(
    () -> {
        SdkCall.execute(() -> {
            // The landmark alarm list containing the landmarks that are to be intercepted
            LandmarkAlarmList landmarkAlarms = alarmService != null ? alarmService.getLandmarkAlarms() : null;

            if (landmarkAlarms != null && landmarkAlarms.size() > 0) {
                // Get the closest landmark and its associated distance (in meters)
                UnlLandmark closestLandmark = landmarkAlarms.getItem(0);
                double distance = landmarkAlarms.getDistance(0);

                if (closestLandmark != null) {
                    Log.i("LandmarkAlarm", "The landmark " + closestLandmark.getName() + " is " + (int) distance + " meters away");
                }
            }
        });
    },
    () -> {
        Log.d("LandmarkAlarm", "UnlLandmark was passed over");

        SdkCall.execute(() -> {
            // The landmarks that were passed over
            LandmarkAlarmList landmarkAlarmsPassedOver = alarmService != null ? alarmService.getLandmarkAlarmsPassedOver() : null;

            // Process the landmarks that were passed over
            if (landmarkAlarmsPassedOver != null) {
                for (int i = 0; i < landmarkAlarmsPassedOver.size(); i++) {
                    UnlLandmark landmark = landmarkAlarmsPassedOver.getItem(i);
                    if (landmark != null) {
                        Log.d("LandmarkAlarm", "Passed landmark: " + landmark.getName());
                    }
                }
            }
        });
    }
);

```

> 📝 **INFO**
>
> The `onLandmarkAlarmsUpdated` callback will be continuously triggered once the threshold distance is exceeded, until the landmark is intercepted. The `onLandmarkAlarmsPassedOver` callback is called once when the landmark is intercepted.

> 🚨 **DANGER**
>
> The items within `landmarkAlarmsPassedOver` do not have a predefined sort order. To identify the most recently passed landmark, you can compare the current list of items with the previous list of `landmarkAlarmsPassedOver` entries.

### Specify the landmarks to be monitored[​](#specify-the-landmarks-to-be-monitored "Direct link to Specify the landmarks to be monitored")

Landmarks can be defined and added to the UnlAlarmService for monitoring. The user will receive notifications through the `onLandmarkAlarmsUpdated` callback when approaching specified landmarks.

* Kotlin
* Java

```kotlin
// Kotlin
// Create landmarks to monitor
val landmark1 = UnlLandmark().apply {
    name = "UnlLandmark 1"
    coordinates = UnlCoordinates(49.0576, 1.9705)
}

val landmark2 = UnlLandmark().apply {
    name = "UnlLandmark 2"
    coordinates = UnlCoordinates(43.7704, 1.2360)
}
//Create a landmark store and add the landmarks to it
UnlLandmarkStoreService().createLandmarkStore("Landmarks to be monitored").also { result ->
    result?.let { (store, err) ->
        if (!UnlError.isError(err))
            store?.let {
                it.addLandmark(landmark1)
                it.addLandmark(landmark2)
                //add the landmark store to the alarm service for monitoring
                alarmService?.landmarkStores?.add(it)
            }
    }
}

```

```java
// Java
// Create landmarks to monitor
UnlLandmark landmark1 = new UnlLandmark();
landmark1.setName("UnlLandmark 1");
landmark1.setCoordinates(new UnlCoordinates(49.0576, 1.9705));

UnlLandmark landmark2 = new UnlLandmark();
landmark2.setName("UnlLandmark 2");
landmark2.setCoordinates(new UnlCoordinates(43.7704, 1.2360));

// Create a landmark store and add the landmarks to it
Pair<UnlLandmarkStore, Integer> result = new UnlLandmarkStoreService().createLandmarkStore("Landmarks to be monitored");
if (result != null) {
    UnlLandmarkStore store = result.first;
    int err = result.second;
    if (!UnlError.isError(err) && store != null) {
        store.addLandmark(landmark1);
        store.addLandmark(landmark2);
        // Add the landmark store to the alarm service for monitoring
        if (alarmService != null) {
            alarmService.getLandmarkStores().add(store);
        }
    }
}

```

Multiple landmark stores can be added to the monitoring list simultaneously. These stores can be updated later by modifying the landmarks within them or removed from the monitoring list as needed.

## Overlay alarms[​](#overlay-alarms "Direct link to Overlay alarms")

The workflow for overlay items is similar to that for landmarks, with comparable behavior and functionality. Notifications for overlay items are triggered in the same way as for landmarks, based on proximity or other criteria. The steps for monitoring, and handling events related to overlay items align with those for landmarks. All the notices specified above for landmarks are also applicable for overlay items.

> 🚨 **DANGER**
>
> To enable overlay alarms, a map view must be created, and a style containing the overlay to be monitored should be applied. Additionally, the overlay needs to be enabled for the alarms to function properly.

### Configure alarms listeners[​](#configure-alarms-listeners-1 "Direct link to Configure alarms listeners")

The following snippet demonstrates how to retrieve the next overlay item to be intercepted and the distance to it and how to detect when a overlay item has been passed:

* Kotlin
* Java

```kotlin
// Kotlin
val alarmListener = UnlAlarmListener.create(
    onOverlayItemAlarmsUpdated = {
        SdkCall.execute {
            // The overlay item alarm list containing the overlay items that are to be intercepted
            val overlayItemAlarms = alarmService?.overlayItemAlarms

            if (overlayItemAlarms != null && overlayItemAlarms.size > 0) {
                // Get the closest overlay item and its associated distance
                val closestOverlayItem = overlayItemAlarms.getItem(0)
                val distance = overlayItemAlarms.getDistance(0)

                closestOverlayItem?.let { overlayItem ->
                    Log.i("OverlayAlarm", "The overlay item is ${distance.toInt()} meters away")
                }
            }
        }
    },
    onOverlayItemAlarmsPassedOver = {
        Log.d("OverlayAlarm", "Overlay item was passed over")

        SdkCall.execute {
            // The overlay items that were passed over
            val overlayItemAlarmsPassedOver = alarmService?.overlayItemAlarmsPassedOver

            // Process the overlay items that were passed over
            overlayItemAlarmsPassedOver?.let { passedAlarms ->
                for (i in 0 until passedAlarms.size) {
                    val overlayItem = passedAlarms.getItem(i)
                    overlayItem?.let {
                        Log.d("OverlayAlarm", "Passed overlay item at distance: ${passedAlarms.getDistance(i)}")
                    }
                }
            }
        }
    }
)

```

```java
// Java
UnlAlarmListener alarmListener = UnlAlarmListener.create(
    () -> {
        SdkCall.execute(() -> {
            // The overlay item alarm list containing the overlay items that are to be intercepted
            OverlayItemAlarmList overlayItemAlarms = alarmService != null ? alarmService.getOverlayItemAlarms() : null;

            if (overlayItemAlarms != null && overlayItemAlarms.size() > 0) {
                // Get the closest overlay item and its associated distance
                OverlayItem closestOverlayItem = overlayItemAlarms.getItem(0);
                double distance = overlayItemAlarms.getDistance(0);

                if (closestOverlayItem != null) {
                    Log.i("OverlayAlarm", "The overlay item is " + (int) distance + " meters away");
                }
            }
        });
    },
    () -> {
        Log.d("OverlayAlarm", "Overlay item was passed over");

        SdkCall.execute(() -> {
            // The overlay items that were passed over
            OverlayItemAlarmList overlayItemAlarmsPassedOver = alarmService != null ? alarmService.getOverlayItemAlarmsPassedOver() : null;

            // Process the overlay items that were passed over
            if (overlayItemAlarmsPassedOver != null) {
                for (int i = 0; i < overlayItemAlarmsPassedOver.size(); i++) {
                    OverlayItem overlayItem = overlayItemAlarmsPassedOver.getItem(i);
                    if (overlayItem != null) {
                        Log.d("OverlayAlarm", "Passed overlay item at distance: " + overlayItemAlarmsPassedOver.getDistance(i));
                    }
                }
            }
        });
    }
);

```

### Specify the overlays to be monitored[​](#specify-the-overlays-to-be-monitored "Direct link to Specify the overlays to be monitored")

The workflow for specifying the overlay items to be monitored is a bit different from the workflow for landmarks. Instead of specifying the landmarks one by one, the overlay items are specified as a whole, based on the overlay and on the overlay categories.

The snippet below shows how to add specific overlay types to be monitored:

* Kotlin
* Java

```kotlin
// Kotlin
// Add safety cameras overlay (speed cameras, red light cameras, etc.)
alarmService?.startAlarmForOverlay(ECommonOverlayId.Safety)

// Add multiple overlay types at once
val overlayIds = arrayListOf(
    ECommonOverlayId.Safety,
    ECommonOverlayId.SocialReports
)
alarmService?.startAlarmForOverlays(overlayIds)

// Alternative: Directly access overlays collection
alarmService?.overlays?.let { overlaysCollection ->
    // Add specific overlay by getting it from OverlayService
    val overlayService = OverlayService()
    val safetyOverlays = overlayService.getOverlaysById(arrayListOf(ECommonOverlayId.Safety))
    overlaysCollection.add(safetyOverlays.first)
}

```

```java
// Java
// Add safety cameras overlay (speed cameras, red light cameras, etc.)
if (alarmService != null) {
    alarmService.startAlarmForOverlay(ECommonOverlayId.Safety);

    // Add multiple overlay types at once
    ArrayList<ECommonOverlayId> overlayIds = new ArrayList<>();
    overlayIds.add(ECommonOverlayId.Safety);
    overlayIds.add(ECommonOverlayId.SocialReports);
    alarmService.startAlarmForOverlays(overlayIds);

    // Alternative: Directly access overlays collection
    OverlayCollection overlaysCollection = alarmService.getOverlays();
    if (overlaysCollection != null) {
        // Add specific overlay by getting it from OverlayService
        OverlayService overlayService = new OverlayService();
        ArrayList<ECommonOverlayId> safetyIds = new ArrayList<>();
        safetyIds.add(ECommonOverlayId.Safety);
        Pair<Overlay, Integer> safetyOverlays = overlayService.getOverlaysById(safetyIds);
        overlaysCollection.add(safetyOverlays.first);
    }
}

```

You can also remove specific overlays from monitoring:

* Kotlin
* Java

```kotlin
// Kotlin
// Stop monitoring safety cameras
alarmService?.stopAlarmForOverlay(ECommonOverlayId.Safety)

// Stop monitoring multiple overlay types
val overlayIdsToRemove = arrayListOf(
    ECommonOverlayId.Safety,
    ECommonOverlayId.SocialReports
)
alarmService?.stopAlarmForOverlays(overlayIdsToRemove)

```

```java
// Java
// Stop monitoring safety cameras
if (alarmService != null) {
    alarmService.stopAlarmForOverlay(ECommonOverlayId.Safety);

    // Stop monitoring multiple overlay types
    ArrayList<ECommonOverlayId> overlayIdsToRemove = new ArrayList<>();
    overlayIdsToRemove.add(ECommonOverlayId.Safety);
    overlayIdsToRemove.add(ECommonOverlayId.SocialReports);
    alarmService.stopAlarmForOverlays(overlayIdsToRemove);
}

```

Here's a complete example demonstrating landmark and overlay alarm monitoring in an Android application:

* Kotlin
* Java

```kotlin
// Kotlin
class LandmarkOverlayAlarmsActivity : AppCompatActivity() {

    private var alarmService: UnlAlarmService? = null
    private lateinit var alarmStatusText: TextView

    private val alarmListener = UnlAlarmListener.create(
        onLandmarkAlarmsUpdated = {
            handleLandmarkAlarmsUpdated()
        },
        onLandmarkAlarmsPassedOver = {
            handleLandmarkAlarmsPassedOver()
        },
        onOverlayItemAlarmsUpdated = {
            handleOverlayAlarmsUpdated()
        },
        onOverlayItemAlarmsPassedOver = {
            handleOverlayAlarmsPassedOver()
        },
        postOnMain = true
    )

    private fun initializeAlarmService() {
        SdkCall.execute {
            alarmService = UnlAlarmService.produce(alarmListener)
            alarmService?.apply {
                // Configure alarm settings
                alarmDistance = 300.0 // 300 meters
                monitorWithoutRoute = true

                // Add landmarks to monitor
                addCustomLandmarks()

                // Add overlay monitoring
                startAlarmForOverlay(ECommonOverlayId.Safety)
                startAlarmForOverlay(ECommonOverlayId.PointsOfInterest)
            }
        }
    }

    private fun addCustomLandmarks() {
        SdkCall.execute {
            alarmService?.landmarkStores?.let { landmarkStores ->
                val defaultStore = landmarkStores.default
                defaultStore?.let { store ->
                    // Add custom landmarks
                    val monument = UnlLandmark().apply {
                        name = "Historic Monument"
                        coordinates = UnlCoordinates(52.3676, 4.9041) // Amsterdam
                    }

                    val viewpoint = UnlLandmark().apply {
                        name = "Scenic Viewpoint"
                        coordinates = UnlCoordinates(52.3702, 4.8952)
                    }

                    store.add(monument)
                    store.add(viewpoint)
                }
            }
        }
    }

    private fun handleLandmarkAlarmsUpdated() {
        SdkCall.execute {
            val landmarkAlarms = alarmService?.landmarkAlarms
            if (landmarkAlarms != null && landmarkAlarms.size > 0) {
                val closestLandmark = landmarkAlarms.getItem(0)
                val distance = landmarkAlarms.getDistance(0)

                closestLandmark?.let { landmark ->
                    runOnUiThread {
                        alarmStatusText.text = "Approaching: ${landmark.name} (${distance.toInt()}m)"
                        alarmStatusText.setTextColor(Color.BLUE)
                    }
                    Log.i("LandmarkAlarm", "Approaching landmark: ${landmark.name} at ${distance}m")
                }
            }
        }
    }

    private fun handleLandmarkAlarmsPassedOver() {
        Log.d("LandmarkAlarm", "UnlLandmark passed")
        runOnUiThread {
            alarmStatusText.text = "UnlLandmark passed"
            alarmStatusText.setTextColor(Color.GREEN)
        }
    }

    private fun handleOverlayAlarmsUpdated() {
        SdkCall.execute {
            val overlayAlarms = alarmService?.overlayItemAlarms
            if (overlayAlarms != null && overlayAlarms.size > 0) {
                val closestOverlay = overlayAlarms.getItem(0)
                val distance = overlayAlarms.getDistance(0)

                closestOverlay?.let { overlayItem ->
                    runOnUiThread {
                        alarmStatusText.text = "Safety camera ahead: ${distance.toInt()}m"
                        alarmStatusText.setTextColor(Color.RED)
                    }
                    Log.w("OverlayAlarm", "Safety camera detected at ${distance}m")

                    // Play warning sound for safety cameras
                    playAlarmSound()
                }
            }
        }
    }

    private fun handleOverlayAlarmsPassedOver() {
        Log.d("OverlayAlarm", "Overlay item passed")
        runOnUiThread {
            alarmStatusText.text = "Safety camera passed"
            alarmStatusText.setTextColor(Color.GRAY)
        }
    }

    private fun playAlarmSound() {
        // Implementation depends on your audio system
        // Example: MediaPlayer, TTS, or notification sounds
    }

    override fun onDestroy() {
        super.onDestroy()
        SdkCall.execute {
            // Clean up monitoring
            alarmService?.stopAlarmForOverlay(ECommonOverlayId.Safety)
            alarmService?.stopAlarmForOverlay(ECommonOverlayId.PointsOfInterest)
            alarmService?.setAlarmListener(null)
        }
        alarmService = null
    }
}

```

```java
// Java
class LandmarkOverlayAlarmsActivity extends AppCompatActivity {

    private UnlAlarmService alarmService = null;
    private TextView alarmStatusText;

    private final UnlAlarmListener alarmListener = UnlAlarmListener.create(
        () -> {
            handleLandmarkAlarmsUpdated();
        },
        () -> {
            handleLandmarkAlarmsPassedOver();
        },
        () -> {
            handleOverlayAlarmsUpdated();
        },
        () -> {
            handleOverlayAlarmsPassedOver();
        },
        true // postOnMain
    );

    private void initializeAlarmService() {
        SdkCall.execute(() -> {
            alarmService = UnlAlarmService.produce(alarmListener);
            if (alarmService != null) {
                // Configure alarm settings
                alarmService.setAlarmDistance(300.0); // 300 meters
                alarmService.setMonitorWithoutRoute(true);

                // Add landmarks to monitor
                addCustomLandmarks();

                // Add overlay monitoring
                alarmService.startAlarmForOverlay(ECommonOverlayId.Safety);
                alarmService.startAlarmForOverlay(ECommonOverlayId.PointsOfInterest);
            }
        });
    }

    private void addCustomLandmarks() {
        SdkCall.execute(() -> {
            if (alarmService != null) {
                LandmarkStoreCollection landmarkStores = alarmService.getLandmarkStores();
                if (landmarkStores != null) {
                    UnlLandmarkStore defaultStore = landmarkStores.getDefault();
                    if (defaultStore != null) {
                        // Add custom landmarks
                        UnlLandmark monument = new UnlLandmark();
                        monument.setName("Historic Monument");
                        monument.setCoordinates(new UnlCoordinates(52.3676, 4.9041)); // Amsterdam

                        UnlLandmark viewpoint = new UnlLandmark();
                        viewpoint.setName("Scenic Viewpoint");
                        viewpoint.setCoordinates(new UnlCoordinates(52.3702, 4.8952));

                        defaultStore.add(monument);
                        defaultStore.add(viewpoint);
                    }
                }
            }
        });
    }

    private void handleLandmarkAlarmsUpdated() {
        SdkCall.execute(() -> {
            LandmarkAlarmList landmarkAlarms = alarmService != null ? alarmService.getLandmarkAlarms() : null;
            if (landmarkAlarms != null && landmarkAlarms.size() > 0) {
                UnlLandmark closestLandmark = landmarkAlarms.getItem(0);
                double distance = landmarkAlarms.getDistance(0);

                if (closestLandmark != null) {
                    runOnUiThread(() -> {
                        alarmStatusText.setText("Approaching: " + closestLandmark.getName() + " (" + (int) distance + "m)");
                        alarmStatusText.setTextColor(Color.BLUE);
                    });
                    Log.i("LandmarkAlarm", "Approaching landmark: " + closestLandmark.getName() + " at " + distance + "m");
                }
            }
        });
    }

    private void handleLandmarkAlarmsPassedOver() {
        Log.d("LandmarkAlarm", "Landmark passed");
        runOnUiThread(() -> {
            alarmStatusText.setText("Landmark passed");
            alarmStatusText.setTextColor(Color.GREEN);
        });
    }

    private void handleOverlayAlarmsUpdated() {
        SdkCall.execute(() -> {
            OverlayItemAlarmList overlayAlarms = alarmService != null ? alarmService.getOverlayItemAlarms() : null;
            if (overlayAlarms != null && overlayAlarms.size() > 0) {
                OverlayItem closestOverlay = overlayAlarms.getItem(0);
                double distance = overlayAlarms.getDistance(0);

                if (closestOverlay != null) {
                    runOnUiThread(() -> {
                        alarmStatusText.setText("Safety camera ahead: " + (int) distance + "m");
                        alarmStatusText.setTextColor(Color.RED);
                    });
                    Log.w("OverlayAlarm", "Safety camera detected at " + distance + "m");

                    // Play warning sound for safety cameras
                    playAlarmSound();
                }
            }
        });
    }

    private void handleOverlayAlarmsPassedOver() {
        Log.d("OverlayAlarm", "Overlay item passed");
        runOnUiThread(() -> {
            alarmStatusText.setText("Safety camera passed");
            alarmStatusText.setTextColor(Color.GRAY);
        });
    }

    private void playAlarmSound() {
        // Implementation depends on your audio system
        // Example: MediaPlayer, TTS, or notification sounds
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        SdkCall.execute(() -> {
            // Clean up monitoring
            if (alarmService != null) {
                alarmService.stopAlarmForOverlay(ECommonOverlayId.Safety);
                alarmService.stopAlarmForOverlay(ECommonOverlayId.PointsOfInterest);
                alarmService.setAlarmListener(null);
            }
        });
        alarmService = null;
    }
}

```

This example demonstrates:

* **Proper initialization** of alarm service with both landmark and overlay monitoring
* **Custom landmark creation** and monitoring setup
* **Overlay monitoring** for safety cameras and points of interest
* **Real-time UI updates** showing alarm status and distances
* **Proper cleanup** in the activity lifecycle
* **Thread-safe operations** using `SdkCall.execute` and `runOnUiThread`
