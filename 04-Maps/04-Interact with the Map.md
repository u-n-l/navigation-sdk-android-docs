# Interact with the map

The Navigation SDK for Android map view natively supports common gestures like pinch and double-tap for zooming. The table below outlines the available gestures and their default behaviors on the map.

| Gesture              | Description           |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tap                  | **Tap the screen with one finger**. This gesture does not have a predefined map action.      |
| Double Tap           | **To zoom the map in by a fixed amount**, tap the screen twice with one finger.       |
| Long Press           | **Press and hold one finger to the screen**. This gesture does not have a predefined map action.                 |
| Pan                  | **To move the map**, press and hold one finger to the screen, and move it in any direction. The map will keep moving with a little momentum after the finger was lifted.     |
| 2 Finger Pan / Shove | **To tilt the map**, press and hold two fingers to the screen, and move them vertically. No behavior is predefined for other directions.          |
| 2 Finger Tap         | **To align map towards north**, tap the screen with two fingers.        |
| Pinch                | **To zoom in or out continuously**, press and hold two fingers to the screen, and increase or decrease the distance between them. **To rotate the map continuously**, press and hold two fingers to the screen, and change the angle between them either by rotating them both or by moving one of them. |

The SDK provides support in `UnlMapView`, for informing whenever the user performs an action that could be detected. Usually, you will want to add a specific behavior to your application after a gesture was detected, like performing a selection after a tap on map.

* Tap: `onTouch`
* Double Tap (one finger taps the same area in quick succession): `onDoubleTouch`
* Two Taps (two fingers tap the screen simultaneously): `onTwoTouches`
* Long Press: `onLongDown`
* Pan: `onMove` obtains the two points between which the movement occurred.
* Shove: `onShove` obtains the angle, and gesture specific points
* Rotate: `onMapAngleUpdated`
* Fling: `onSwipe`
* Pinch: `onPinch`

The user can also listen for composite gestures:

* Tap followed by a pan: `onTouchMove`
* Pinch followed by a swipe: `onPinchSwipe`
* Tap followed by a pinch: `onTouchPinch`
* Two double touches: `onTwoDoubleTouches`

> 🚨 **Danger**
>
>Keep in mind that only one listener can be active at a time for a specific gesture. If multiple listeners are registered, only the most recently set listener will be invoked when the gesture is detected.

Use `isCameraMoving()` to check if the map camera is currently moving.

## Enable and disable gestures[​](#enable-and-disable-gestures "Direct link to Enable and disable gestures")

Touch gestures can be disabled or enabled by calling `enableTouchGestures` method like so:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

mapView?.preferences?.enableTouchGestures(
    ETouchGestures.OnTouch.value or ETouchGestures.OnMove.value,
    false
)

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

if (mapView != null && mapView.getPreferences() != null) {
    mapView.getPreferences().enableTouchGestures(
        ETouchGestures.OnTouch.getValue() | ETouchGestures.OnMove.getValue(),
        false
    );
}

```

> 📝 **Info**
>
> The desired gestures in the `ETouchGestures` enum can be enabled or disabled by setting the `enabled` parameter to `true` or `false`, respectively. By default, all gestures are enabled.

The `ETouchGestures` enum supports the following gesture types:

* *Basic Touch*: `OnTouch`, `OnLongDown`, `OnDoubleTouch`, `OnTwoPointersTouch`, `OnTwoPointersDoubleTouch`
* *Movement*: `OnMove`, `OnTouchMove`, `OnSwipe`
* *Pinch and Rotation*: `OnPinchSwipe`, `OnPinch`, `OnRotate`, `OnShove`
* *Combined Gestures*: `OnTouchPinch`, `OnTouchRotate`, `OnTouchShove`, `OnRotatingSwipe`

For checking if a gesture is enabled, the `isTouchGestureEnabled` method can be used:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val isTouchEnabled = mapView?.preferences?.isTouchGestureEnabled(ETouchGestures.OnTouch) ?: false

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

boolean isTouchEnabled = mapView != null && mapView.getPreferences() != null ? mapView.getPreferences().isTouchGestureEnabled(ETouchGestures.OnTouch) : false;

```

## Implement gesture listeners[​](#implement-gesture-listeners "Direct link to Implement gesture listeners")

Let's see an example of how gesture listeners can be registered. The `UnlMapView` provides specific listeners for each gesture. As soon as you register a listener, it will receive all related events for that gesture via the dedicated callback.

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

// Set up gesture listeners
mapView?.onMapAngleUpdated = { angle ->
    Log.d("Gesture", "onMapAngleUpdated: $angle")
}

mapView?.onTouch = { point ->
    Log.d("Gesture", "onTouch: $point")
}

mapView?.onMove = { start, end ->
    Log.d("Gesture", "onMove from (${start.x}, ${start.y}) to (${end.x}, ${end.y})")
}

mapView?.onLongDown = { point ->
    Log.d("Gesture", "onLongDown: $point")
}

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

// Set up gesture listeners
if (mapView != null) {
    mapView.setOnMapAngleUpdated(angle -> {
        Log.d("Gesture", "onMapAngleUpdated: " + angle);
    });

    mapView.setOnTouch(point -> {
        Log.d("Gesture", "onTouch: " + point);
    });

    mapView.setOnMove((start, end) -> {
        Log.d("Gesture", "onMove from (" + start.x + ", " + start.y + ") to (" + end.x + ", " + end.y + ")");
    });

    mapView.setOnLongDown(point -> {
        Log.d("Gesture", "onLongDown: " + point);
    });
}

```

> **Danger**
>
> Executing resource-intensive tasks within map-related callbacks can degrade performance.

## Implement map render listeners[​](#implement-map-render-listeners "Direct link to Implement map render listeners")

The `onViewportResized` listener allows you to monitor when the map's viewport dimensions change. This can occur when the user resizes the application window or changes the orientation of the device. In this callback, you receive a `UnlRect` object representing the new viewport size.

* Kotlin
* Java

```kotlin
// Kotlin
mapView?.onViewportResized = { rect ->
    Log.d("Viewport", "Viewport resized to: ${rect.width}x${rect.height}")
}

```

```java
// Java
if (mapView != null) {
    mapView.setOnViewportResized(rect -> {
        Log.d("Viewport", "Viewport resized to: " + rect.getWidth() + "x" + rect.getHeight());
    });
}

```

Use cases include:

* Adjusting overlays or UI elements to fit the new viewport size.
* Triggering animations or updates based on the map's dimensions.

The `onViewRendered` listener is triggered after the map completes a rendering cycle. This listener provides `EViewDataTransitionStatus` and `EViewCameraTransitionStatus` parameters containing details about the rendering process.

* Kotlin
* Java

```kotlin
// Kotlin
mapView?.onViewRendered = { dataStatus, cameraStatus ->
    Log.d("Render", "View rendered - Data: $dataStatus, Camera: $cameraStatus")
}

```

```java
// Java
if (mapView != null) {
    mapView.setOnViewRendered((dataStatus, cameraStatus) -> {
        Log.d("Render", "View rendered - Data: " + dataStatus + ", Camera: " + cameraStatus);
    });
}

```

## Map selection functionality[​](#map-selection-functionality "Direct link to Map selection functionality")

After detecting a gesture, such as a tap, usually some specific action like selecting a landmark or a route is performed on the UnlMapView. This selection is made using a map cursor, which is invisible by default. To showcase its functionality, the cursor can be made visible using the `UnlMapViewPrefernces` setting:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

// Enable cursor (default is false)
mapView?.preferences?.enableCursor = true
// Enable cursor to render on screen
mapView?.preferences?.enableCursorRender = true

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

// Enable cursor (default is false)
if (mapView != null && mapView.getPreferences() != null) {
    mapView.getPreferences().setEnableCursor(true);
    // Enable cursor to render on screen
    mapView.getPreferences().setEnableCursorRender(true);
}

```

Doing this will result in a crosshair-like icon in center of screen.

![Displaying a cursor](/assets/images/example_android_display_cursor_street_name1-e51059b22fec264d7359553f06dcb2e7.png "Displaying a cursor")

**Displaying a cursor**

### Landmark selection[​](#landmark-selection "Direct link to Landmark selection")

To get the selected landmarks, you can use the following code:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

mapView?.onTouch = { pos ->
    // Set the cursor position
    mapView.cursorScreenPosition = pos

    // Get the landmarks at the cursor position
    val landmarks = mapView.cursorSelectionLandmarks

    landmarks?.forEach { landmark ->
        // handle landmark
        Log.d("Selection", "Selected landmark: ${landmark.name}")
    }
}

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

if (mapView != null) {
    mapView.setOnTouch(pos -> {
        // Set the cursor position
        mapView.setCursorScreenPosition(pos);

        // Get the landmarks at the cursor position
        UnlLandmarkList landmarks = mapView.getCursorSelectionLandmarks();

        if (landmarks != null) {
            for (UnlLandmark landmark : landmarks) {
                // handle landmark
                Log.d("Selection", "Selected landmark: " + landmark.getName());
            }
        }
    });
}

```

> 📝 **Info**
>
> At higher zoom levels, landmarks provided by the `cursorSelectionLandmarks` property may lack some details for optimization purposes. Use `UnlSearchService` to retrieve full landmark details if needed.

To unregister the callback:

* Kotlin
* Java

```kotlin
// Kotlin
mapView?.onTouch = null

```

```java
// Java
if (mapView != null) {
    mapView.setOnTouch(null);
}

```

> 📝 **Info**
>
> The selected landmarks are returned by the `cursorSelectionLandmarks` property, which is accessed after updating the cursor's position. This step is essential because the SDK only detects landmarks that are positioned directly under the cursor.

> 🚨 **Danger**
>
> The cursor screen position is also used for determining the default screen position for centering (unless other values are specified). Modifying the screen position might change the behavior of centering in unexpected ways.

### Street selection[​](#street-selection "Direct link to Street selection")

The following code can be used to return selected streets under the cursor:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

// Register touch callback to set cursor to tapped position
mapView?.onTouch = { point ->
    mapView.cursorScreenPosition = point
    val streets = mapView.cursorSelectionStreets

    val currentStreetName = if (streets?.isEmpty() != false) "Unnamed street" else streets.first().name
    Log.d("Selection", "Street name: $currentStreetName")
}

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

// Register touch callback to set cursor to tapped position
if (mapView != null) {
    mapView.setOnTouch(point -> {
        mapView.setCursorScreenPosition(point);
        UnlLandmarkList streets = mapView.getCursorSelectionStreets();

        String currentStreetName = (streets == null || streets.isEmpty()) ? "Unnamed street" : streets.get(0).getName();
        Log.d("Selection", "Street name: " + currentStreetName);
    });
}

```

Street name can then be displayed on screen. This is the result:

![Displaying a cursor selected street name](/assets/images/example_android_display_cursor_street_name2-1bf8213e189b1f7abb8c270f981cfcbc.png "Displaying a cursor selected street name")

**Displaying a cursor selected street name**

<br/>

> 📝 **Info**
>
> The visibility of the cursor has no impact whatsoever on the selection logic.

Getting the current cursor screen position is done by accessing the `cursorScreenPosition` property of `UnlMapView`.

### List of selection types[​](#list-of-selection-types "Direct link to List of selection types")

To summarize, there are multiple properties used to select different types of elements on the map. You can see all those in the following table.

| Entity         | Select method                  | Result type         | Observations                                     |
| -------------- | ------------------------------ | ------------------- | ------------------------------------------------ |
| UnlLandmark       | `cursorSelectionLandmarks`     | `UnlLandmarkList?`     |                                                  |
| UnlMarker         | `cursorSelectionMarkers`       | `MarkerMatchList?`  | Returns `MarkerMatchList`, not a `MarkerList`    |
| OverlayItem    | `cursorSelectionOverlayItems`  | `OverlayItemList?`  |                                                  |
| Street         | `cursorSelectionStreets`       | `UnlLandmarkList?`     | Streets are handled as landmarks                 |
| UnlRoute          | `cursorSelectionRoutes`        | `UnlRouteList?`        |                                                  |
| UnlPath           | `cursorSelectionPath`          | `UnlPath?`             | Null is returned if no path is found             |
| MapSceneObject | `cursorSelectionSceneObject`   | `MapSceneObject?`   | Null is returned if no map scene object is found |
| UnlTrafficEvent   | `cursorSelectionTrafficEvents` | `TrafficEventList?` |                                                  |

As you can see, when selecting markers a `MarkerMatchList` is returned. The match specifies information about the matched marker (the marker collection in which the marker resides, the index of the marker in the collection, the matched part index, the matched index of the point in the part).

You can also register callbacks that are called when the cursor is placed over elements on the `UnlMapView` class:

* `onCursorSelectionUpdatedLandmarks` for landmarks
* `onCursorSelectionUpdatedMarkerMatch` for markers
* `onCursorSelectionUpdatedOverlayItems` for overlay items
* `onCursorSelectionUpdatedRoutes` for routes
* `onCursorSelectionUpdatedTrafficEvents` for traffic events

These callbacks are triggered whenever the selection changes - for example, when new elements are selected, the selection switches to different elements, or the selection is cleared (in which case the callback is invoked with null or an empty list).

To unregister a callback, simply set the corresponding property to null.

## Capture the map view as an image[​](#capture-the-map-view-as-an-image "Direct link to Capture the map view as an image")

In certain situations, it may be necessary to save the map as an image - for example, to generate previews that are too expensive to redraw in real time. The Navigation SDK for Android provides a dedicated method for capturing the map view through the `UnlMapSurfaceView`.

* Kotlin
* Java

```kotlin
// Kotlin
// Using UnlMapSurfaceView's takeScreenshot method
val surface = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
surface.takeScreenshot { bitmap ->
    // Convert to byte array if needed
    val stream = ByteArrayOutputStream()
    bitmap.compress(Bitmap.CompressFormat.JPEG, 90, stream)
    val imageBytes = stream.toByteArray()
}

```

```java
// Java
// Using UnlMapSurfaceView's takeScreenshot method
UnlMapSurfaceView surface = findViewById(R.id.gem_surface);
surface.takeScreenshot(bitmap -> {
    // Convert to byte array if needed
    ByteArrayOutputStream stream = new ByteArrayOutputStream();
    bitmap.compress(Bitmap.CompressFormat.JPEG, 90, stream);
    byte[] imageBytes = stream.toByteArray();
});

```

> 💡 **Tip**
>
> To ensure that any ongoing map animations or loading have completed, wait for the `onViewRendered` callback to be triggered with data transition status set to `EViewDataTransitionStatus.Complete` before capturing the image. Make sure to implement a timeout, as the `onViewRendered` is only triggered when the map is rendering - and will not be called if everything is already loaded.
