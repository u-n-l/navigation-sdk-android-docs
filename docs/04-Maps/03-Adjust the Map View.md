# Adjust the map view

The Navigation SDK for Android provides multiple ways to modify the map view, center on coordinates or areas, including `UnlMapView` functionality for exploring different perspectives.

The SDK enables a range of features, including zooming in and out, tilting the camera, centering on specific locations, and more, all through the `UnlMapView` and `UnlMapViewPrefernces` classes provided by the SDK.

* Use `viewport` property to return the current viewport of the map view.
* Center on specific coordinates with `centerOnCoordinates` which takes extra parameters for centering preferences such as zoom level, screen position, map angle, animation and more.
* Center on a specific geographic area represented by a rectangle with coordinates as corners with `centerOnArea` and `centerOnRectArea`.
* Align map according to the north direction by using `alignNorthUp`.
* Adjust the current zoom level using `setZoomLevel`, where lower values result in a more zoomed-out view.
* Perform a scrolling behavior based on horizontal and vertical scroll offsets with `scroll` method.
* `MapCamera` class provides further functionality such as manipulating orientation, position, and state.

## Map viewport[​](#map-viewport "Direct link to Map viewport")

The map viewport refers to the area displayed by the `UnlMapView`. Getting the current viewport provides a `UnlRect` object which consists of x, y (left and top) screen coordinates and width and height.

The top left coordinate of the screen is represented by \[0, 0] and bottom right \[`viewport.width`, `viewport.height`].

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val currentViewport = mapView?.viewport

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

UnlRect currentViewport = mapView != null ? mapView.getViewport() : null;

```

This viewport can be useful when you need to use methods such as `centerOnRectArea`.

> 📝 **Info**
>
>The width and height of the map view is measured in physical pixels. To transform them into logical pixels you may need to consider the device's pixel density.

## Map centering[​](#map-centering "Direct link to Map centering")

Map centering can be achieved using the `centerOnCoordinates`, `centerOnArea`, `centerOnRectArea`, `centerOnRoute`, `centerOnDistRoute`, `centerOnRouteInstruction`, `centerOnRouteTrafficEvent` methods.

### Map centering on coordinates[​](#map-centering-on-coordinates "Direct link to Map centering on coordinates")

In order to center the [WGS](https://en.wikipedia.org/wiki/World_Geodetic_System) coordinates on the viewport coordinates you can use the `centerOnCoordinates` method like so:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

mapView?.centerOnCoordinates(UnlCoordinates(45.0, 25.0))

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

if (mapView != null) {
    mapView.centerOnCoordinates(new UnlCoordinates(45.0, 25.0));
}

```

A linear animation can be incorporated while centering, as demonstrated below:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

mapView?.centerOnCoordinates(
    UnlCoordinates(52.14569, 1.0615),
    animation = Animation(EAnimation.Linear, 2000)
)

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

if (mapView != null) {
    mapView.centerOnCoordinates(
        new UnlCoordinates(52.14569, 1.0615),
        new Animation(EAnimation.Linear, 2000)
    );
}

```

> 💡 **Tip**
>
> You can call the `skipAnimation()` method of `UnlMapView` to bypass the animation. To check if an animation is in progress the `isAnimationInProgress()` method can be used. To check if the camera is moving (as a consequence of an animation or not), the `isCameraMoving()` method can be used.

> 🚨 **Danger**
>
> Do not confuse the `zoomLevel` with the `slippyZoomLevel`. The `slippyZoomLevel` is a value linked with the tile system.

### Converting between screen and WGS coordinates[​](#converting-between-screen-and-wgs-coordinates "Direct link to Converting between screen and WGS coordinates")

In order to convert a screen position to WGS coordinates, the `UnlMapView.transformScreenToWgs()` method is used:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val coordsToCenter = mapView?.transformScreenToWgs(UnlXy(pos.x, pos.y))

coordsToCenter?.let {
    mapView?.centerOnCoordinates(it, zoomLevel = 70)
}

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

UnlCoordinates coordsToCenter = mapView != null ? mapView.transformScreenToWgs(new UnlXy(pos.x, pos.y)) : null;

if (coordsToCenter != null && mapView != null) {
    mapView.centerOnCoordinates(coordsToCenter, 70);
}

```

> 📝 **Info**
>
> If the applied style includes elevation and terrain data is loaded, the `transformScreenToWgs` method returns `UnlCoordinates` objects that include altitude.

To convert WGS coordinates to screen coordinates, the `UnlMapView.transformWgsToScreen()` method is used:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val wgsCoordinates = UnlCoordinates(8.0, 25.0)

val screenPosition = mapView?.transformWgsToScreen(wgsCoordinates)

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

UnlCoordinates wgsCoordinates = new UnlCoordinates(8.0, 25.0);

UnlXy screenPosition = mapView != null ? mapView.transformWgsToScreen(wgsCoordinates) : null;

```

> 💡 **Tip**
>
> In order to convert a screen rectangle to a list of WGS geographic areas, use `UnlMapView.transformScreenToWgsListArea` method.

This centers the view precisely on the specified coordinates, positioning them at the cursor position (which by default is in the center of the screen).

### Map centering on coordinates at given screen position[​](#map-centering-on-coordinates-at-given-screen-position "Direct link to Map centering on coordinates at given screen position")

To center on a different area of the viewport (not the cursor position), provide an `xy` parameter, represented as an `UnlXy`. Note that `x` coordinate should be in \[0, `viewport.width`] and `y` coordinate between \[0, `viewport.height`].

> 🚨 **Danger**
>
> The `xy` parameter is defined in physical pixels.

The following example demonstrates how to center the map at one-third of its height:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val physicalHeightPixels = mapView?.viewport?.height ?: 0
val physicalWidthPixels = mapView?.viewport?.width ?: 0

mapView?.centerOnCoordinates(
    UnlCoordinates(52.48209, -2.48888),
    zoomLevel = 40,
    xy = UnlXy(physicalWidthPixels / 2, physicalHeightPixels / 3)
)

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

int physicalHeightPixels = mapView != null && mapView.getViewport() != null ? mapView.getViewport().getHeight() : 0;
int physicalWidthPixels = mapView != null && mapView.getViewport() != null ? mapView.getViewport().getWidth() : 0;

if (mapView != null) {
    mapView.centerOnCoordinates(
        new UnlCoordinates(52.48209, -2.48888),
        40, // zoomLevel
        new UnlXy(physicalWidthPixels / 2, physicalHeightPixels / 3)
    );
}

```

![Centered at one-third of map height](/assets/images/example_android_center_coordinates1-853e1f8276fc6928f48b7ce0e5b30686.png "Centered at one-third of map height")

**Centered at one-third of map height**

> 💡 **Tip**
>
> More parameters such as `animation`, `mapAngle`, `viewAngle` and `zoomLevel` can be passed to the method in order to achieve a higher level of control.

### Map centering on area[​](#map-centering-on-area "Direct link to Map centering on area")

Centering can be done on a specific `RectangleGeographicArea` which consists of top left and bottom right coordinates.

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val topLeftCoords = UnlCoordinates(44.93343, 25.09946)
val bottomRightCoords = UnlCoordinates(44.93324, 25.09987)
val area = RectangleGeographicArea(topLeftCoords, bottomRightCoords)

mapView?.centerOnArea(area)

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

UnlCoordinates topLeftCoords = new UnlCoordinates(44.93343, 25.09946);
UnlCoordinates bottomRightCoords = new UnlCoordinates(44.93324, 25.09987);
RectangleGeographicArea area = new RectangleGeographicArea(topLeftCoords, bottomRightCoords);

if (mapView != null) {
    mapView.centerOnArea(area);
}

```

This will center the view on the geographic area ensuring the `RectangleGeographicArea` covers most of the viewport. For centering the geographic area on a particular coordinate of the viewport, the `xy` parameter, represented as an `UnlXy` should be provided.

Alternatively, to center the `RectangleGeographicArea` on a specific region of the viewport, you can use the `centerOnRectArea` method. This requires passing the `viewRc` parameter, represented as a `UnlRect`, which defines the targeted region of the screen. The `UnlRect` passed to the `viewRc` parameter determines the positioning of the centered area relative to the top-left coordinates. Consequently, the bottom-right corner will be at `left` + `UnlRect`'s width and `top` + `UnlRect`'s height.

> 📝 **Info**
>
> As the width and height of `UnlRect` decrease, the centering will result in a more zoomed-out view. For a more zoomed-in perspective, use larger values within the range \[1, viewport.width - x] and \[1, viewport.height - y].

> 💡 **Tip**
>
> Use the `getOptimalRoutesCenterViewport` and `getOptimalHighlightCenterViewport` methods to compute the viewport region that best fits given routes and highlights.

### Map centering on area with padding[​](#map-centering-on-area-with-padding "Direct link to Map centering on area with padding")

Centering on an area using padding can be done by altering the screen coordinates (in physical pixels) by adding/subtracting the padding value. Then a new `RectangleGeographicArea` object needs to be instantiated with the padded screen coordinates transformed into WGS coordinates using `UnlMapView.transformScreenToWgs(xy)`.

The following code exemplifies the process:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

// Getting the RectangleGeographicArea in which the route belongs
val routeArea = route.geographicArea
val paddingPixels = 200

// Getting the top left point screen coordinates in physical pixels
val routeAreaTopLeftPoint = mapView?.transformWgsToScreen(routeArea.topLeft)

// Adding padding by shifting point in the top left
val topLeftPadded = routeAreaTopLeftPoint?.let { point ->
    UnlXy(
        point.x - paddingPixels,
        point.y - paddingPixels
    )
}

val routeAreaBottomRightPoint = mapView?.transformWgsToScreen(routeArea.bottomRight)

// Adding padding by shifting point downwards three times the padding
val bottomRightPadded = routeAreaBottomRightPoint?.let { point ->
    UnlXy(
        point.x + paddingPixels,
        point.y + 3 * paddingPixels
    )
}

// Converting points with padding to WGS coordinates
val paddedTopLeftCoordinate = topLeftPadded?.let { mapView?.transformScreenToWgs(it) }
val paddedBottomRightCoordinate = bottomRightPadded?.let { mapView?.transformScreenToWgs(it) }

if (paddedTopLeftCoordinate != null && paddedBottomRightCoordinate != null) {
    mapView?.centerOnArea(
        RectangleGeographicArea(
            paddedTopLeftCoordinate,
            paddedBottomRightCoordinate
        )
    )
}

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

// Getting the RectangleGeographicArea in which the route belongs
RectangleGeographicArea routeArea = route.getGeographicArea();
int paddingPixels = 200;

// Getting the top left point screen coordinates in physical pixels
UnlXy routeAreaTopLeftPoint = mapView != null ? mapView.transformWgsToScreen(routeArea.getTopLeft()) : null;

// Adding padding by shifting point in the top left
UnlXy topLeftPadded = null;
if (routeAreaTopLeftPoint != null) {
    topLeftPadded = new UnlXy(
        routeAreaTopLeftPoint.x - paddingPixels,
        routeAreaTopLeftPoint.y - paddingPixels
    );
}

UnlXy routeAreaBottomRightPoint = mapView != null ? mapView.transformWgsToScreen(routeArea.getBottomRight()) : null;

// Adding padding by shifting point downwards three times the padding
UnlXy bottomRightPadded = null;
if (routeAreaBottomRightPoint != null) {
    bottomRightPadded = new UnlXy(
        routeAreaBottomRightPoint.x + paddingPixels,
        routeAreaBottomRightPoint.y + 3 * paddingPixels
    );
}

// Converting points with padding to WGS coordinates
UnlCoordinates paddedTopLeftCoordinate = topLeftPadded != null && mapView != null ? mapView.transformScreenToWgs(topLeftPadded) : null;
UnlCoordinates paddedBottomRightCoordinate = bottomRightPadded != null && mapView != null ? mapView.transformScreenToWgs(bottomRightPadded) : null;

if (paddedTopLeftCoordinate != null && paddedBottomRightCoordinate != null && mapView != null) {
    mapView.centerOnArea(
        new RectangleGeographicArea(
            paddedTopLeftCoordinate,
            paddedBottomRightCoordinate
        )
    );
}

```

![Route without padding](/assets/images/example_android_area_without_padding-3d5f7629b87218a4b791f6bac186c8df.png "Route without padding")

**Route without padding**

![Route with center padding](/assets/images/example_android_area_with_padding-c8433c256f28fc4c50b343ecf80ec86e.png "UnlRoute with center padding")

**Route with center padding**

<br />

> 🚨 **Danger**
>
> When applying padding, note that the height is measured in physical pixels. A conversion may be required depending on your use case.

## Map zoom[​](#map-zoom "Direct link to Map zoom")

To get the current zoom level use the `zoomLevel` property. A bigger value means the camera is closer to the terrain. Changing the zoom level is done through the `setZoomLevel` method of the `UnlMapView` class in the following way:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val zoomLevel = mapView?.zoomLevel
mapView?.setZoomLevel(50)

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

Integer zoomLevel = mapView != null ? mapView.getZoomLevel() : null;
if (mapView != null) {
    mapView.setZoomLevel(50);
}

```

The maximum allowed zoom level can be accessed via the `maxZoomLevel` property from the `UnlMapView` class. In order to check if a particular zoom level can be applied, check if it's within the valid range.

## Map rotation angle[​](#map-rotation-angle "Direct link to Map rotation angle")

To get the current rotation angle of the map, use the `rotationAngle` property from the `UnlMapViewPrefernces` class. Changing the rotation angle is done through the `rotationAngle` setter of `UnlMapViewPrefernces` like so:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val rotationAngle = mapView?.preferences?.rotationAngle ?: 0.0
mapView?.preferences?.rotationAngle = 45.0

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

double rotationAngle = mapView != null && mapView.getPreferences() != null ? mapView.getPreferences().getRotationAngle() : 0.0;
if (mapView != null && mapView.getPreferences() != null) {
    mapView.getPreferences().setRotationAngle(45.0);
}

```

The provided value needs to be between 0 and 360. By default, the camera has a rotation angle value of 0 degrees corresponding to the north-up alignment. Note that the rotation axis is always perpendicular to the ground and passes through the camera, regardless of the current camera orientation.

## Map view angle[​](#map-view-angle "Direct link to Map view angle")

The camera can transform the flat 2D map into a 3D perspective, allowing you to view features like distant roads appearing on the horizon. By default, the camera has a top-down perspective (viewAngle = 90°).

In addition to adjusting the camera's view angle, you can modify its tilt angle. The `tiltAngle` is defined as the complement of the `viewAngle`, calculated as `tiltAngle = 90-viewAngle`

In order to change the view angle of camera you need to access the `preferences` field of `UnlMapView` like so:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val viewAngle = mapView?.preferences?.viewAngle ?: 30
mapView?.preferences?.setViewAngle(45)

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

int viewAngle = mapView != null && mapView.getPreferences() != null ? mapView.getPreferences().getViewAngle() : 30;
if (mapView != null && mapView.getPreferences() != null) {
    mapView.getPreferences().setViewAngle(45);
}

```

![Map with 60 view angle](/assets/images/example_android_map_perspective2-6954172f469464edf0fbb7b1f0c6e81c.png "Map with a view angle of 60 degrees")

**Map with a view angle of 60 degrees**

![Map with 0 view angle](/assets/images/example_android_map_perspective1-9fdfe174c35c8d75411182803654b551.png "Map with a view angle of 0 degrees")

**Map with a view angle of 0 degrees**

<br />

To adjust the camera's perspective dynamically, you can utilize both the `tiltAngle` and `viewAngle` properties.

The difference between the different types of angles is shown below:

> ⚠️ **DANGER** **{TODO} Image/Video missing in original document**

**Tilt angle & view angle**

**Rotation angle**

<br />

> 📝 **Info**
>
> Keep in mind that adjusting the rotation value produces different outcomes depending on the camera's tilt. When the camera is tilted, changing the rotation will shift the target location, whereas with no tilt, the target location remains fixed.

## Map perspective[​](#map-perspective "Direct link to Map perspective")

Map perspective can be either two dimensional or three dimensional and can also be set by using `UnlMapViewPrefernces` method `setMapViewPerspective`:

* Kotlin
* Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val perspective = mapView?.preferences?.mapViewPerspective ?: EMapViewPerspective.ThreeDimensional
mapView?.preferences?.setMapViewPerspective(EMapViewPerspective.ThreeDimensional)

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

EMapViewPerspective perspective = mapView != null && mapView.getPreferences() != null ? mapView.getPreferences().getMapViewPerspective() : EMapViewPerspective.ThreeDimensional;
if (mapView != null && mapView.getPreferences() != null) {
    mapView.getPreferences().setMapViewPerspective(EMapViewPerspective.ThreeDimensional);
}

```

By default, the map perspective is three-dimensional.

![Two dimensional map](/assets/images/example_android_map_perspective3-18356dff297d2fa86e6c5d67a5d1f260.png "Map with a two-dimensional perspective")


**Map with a two-dimensional perspective**

![Three dimensional map](/assets/images/example_android_map_perspective4-24db7e5d4fe3aea3a9f8eb27ce207119.png "Map with a three-dimensional perspective")

**Map with a three-dimensional perspective**

<br />

A three-dimensional perspective gives buildings a realistic, 3D appearance, while a two-dimensional perspective makes them appear as flat shapes.

> 📝 **Info**
>
> To ensure three-dimensional buildings are visible, the camera angle should not be perpendicular to the map. Instead, the view angle must be less than 90 degrees.

The same effect can be implemented more precisely using the `tiltAngle`/`viewAngle` fields.

## Building visibility[​](#building-visibility "Direct link to Building visibility")

Building visibility can be controlled using the `buildingsVisibility` property from the `UnlMapViewPrefernces` class:

* `EBuildingsVisibility.Default`: Uses the default visibility defined in the map style.
* `EBuildingsVisibility.Hide`: Hides all buildings.
* `EBuildingsVisibility.TwoDimensional`: Displays buildings as flat 2D polygons without height.
* `EBuildingsVisibility.ThreeDimensional`: Displays buildings as 3D polygons with height.

- Kotlin
- Java

```kotlin
// Kotlin
// Get UnlMapView from UnlMapSurfaceView
val UnlMapSurfaceView = findViewById<UnlMapSurfaceView>(R.id.gem_surface)
val mapView = UnlMapSurfaceView.mapView

val visibility = mapView?.preferences?.buildingsVisibility ?: EBuildingsVisibility.Default
mapView?.preferences?.buildingsVisibility = EBuildingsVisibility.TwoDimensional

```

```java
// Java
// Get UnlMapView from UnlMapSurfaceView
UnlMapSurfaceView UnlMapSurfaceView = findViewById(R.id.gem_surface);
UnlMapView mapView = UnlMapSurfaceView.getMapView();

EBuildingsVisibility visibility = mapView != null && mapView.getPreferences() != null ? mapView.getPreferences().getBuildingsVisibility() : EBuildingsVisibility.Default;
if (mapView != null && mapView.getPreferences() != null) {
    mapView.getPreferences().setBuildingsVisibility(EBuildingsVisibility.TwoDimensional);
}

```

Buildings become visible when the camera is zoomed in close to the ground. The 3D effect is most noticeable when viewed from a tilted angle. Note that the 3D buildings do not reflect realistic or accurate heights.

## Store and restore a view[​](#store-and-restore-a-view "Direct link to Store and restore a view")

The map camera object has properties and methods for position and orientation ensuring a high level of control over the map view.

For storing a particular view the `saveCameraState` method can be used. This method returns a `DataBuffer` object and depending on the use case this can be stored inside a variable or serialized to a file.

* Kotlin
* Java

```kotlin
// Kotlin
val state = mapView.camera?.saveCameraState()

```

```java
// Java
DataBuffer state = mapView.getCamera() != null ? mapView.getCamera().saveCameraState() : null;

```

Restoring a saved view can be done easily using the `restoreCameraState` method:

* Kotlin
* Java

```kotlin
// Kotlin
state?.let {
    mapView.camera?.restoreCameraState(it)
}

```

```java
// Java
if (state != null && mapView.getCamera() != null) {
    mapView.getCamera().restoreCameraState(state);
}

```

Alternatively the `position` and `orientation` can be stored and restored separately using the provided properties.

> 📝 **Info**
>
> Please note that `saveCameraState` does not contain information about the current style.

## Download individual map tiles[​](#download-individual-map-tiles "Direct link to Download individual map tiles")

A map tile is a small, rectangular image or data chunk that represents a specific geographic area at a particular zoom level on a `UnlMapView`. Tiles are usually downloaded when panning or zooming in on a map, and they are used to render the map's visual content. However, you can also download tiles that are not currently visible on the screen, using the `UnlMapDownloaderService` class.

### Configuring the UnlMapDownloaderService[​](#configuring-the-mapdownloaderservice "Direct link to Configuring the UnlMapDownloaderService")

The service can be configured by setting specific maximum area size in square kilometers to download by using the `maxSquareKm` property:

* Kotlin
* Java

```kotlin
// Kotlin
val service = UnlMapDownloaderService()

// Set a new value
service.maxSquareKm = 100

// Verify the new value
val updatedMaxSquareKm = service.maxSquareKm

```

```java
// Java
UnlMapDownloaderService service = new UnlMapDownloaderService();

// Set a new value
service.setMaxSquareKm(100);

// Verify the new value
int updatedMaxSquareKm = service.getMaxSquareKm();

```

The larger the area, the more tiles can be downloaded, which can lead to increased memory usage. The default value is 1000 square kilometers.

> 🚨 **Danger**
>
> If the `RectangleGeographicArea` surface exceeds the `maxSquareKm`, the `UnlMapDownloaderService` will return `UnlError.OutOfRange`.

Downloading tiles is done by calling the `startDownload` method of `UnlMapDownloaderService` like so:

* Kotlin
* Java

```kotlin
// Kotlin
val service = UnlMapDownloaderService()

service.maxSquareKm = 300

val areas = arrayListOf(
    // Area in which the tiles will be downloaded that is under 300 square kilometers
    RectangleGeographicArea(
        UnlCoordinates(67.69866, 24.81115),
        UnlCoordinates(67.58326, 25.36093)
    )
)

service.startDownload(areas, object : UnlProgressListener() {
    override fun notifyComplete(errorCode: ErrorCode, hint: String) {
        // Handle completion
    }
})

```

```java
// Java
UnlMapDownloaderService service = new UnlMapDownloaderService();

service.setMaxSquareKm(300);

ArrayList<RectangleGeographicArea> areas = new ArrayList<>();
// Area in which the tiles will be downloaded that is under 300 square kilometers
areas.add(new RectangleGeographicArea(
    new UnlCoordinates(67.69866, 24.81115),
    new UnlCoordinates(67.58326, 25.36093)
));

service.startDownload(areas, new UnlProgressListener() {
    @Override
    public void notifyComplete(ErrorCode errorCode, String hint) {
        // Handle completion
    }
});

```

When tiles are downloaded, the `notifyComplete` callback is invoked with an `ErrorCode` parameter indicating the success or failure of the operation. If the download is successful, the error will be `ErrorCode.Success`. Downloaded tiles are stored in the cache and can be used later for features such as viewing map content without requiring an internet connection.

![Maptiles centered in the middle](/assets/images/example_android_downloaded_tiles-736bc4a9c8e1f95cac393f1c73b7c408.png "Maptiles centered in the middle")

**Downloaded tiles centered in the middle, top and bottom tiles are not available**

> 📝 **Info**
>
> UnlSearchService.search method will return an error when trying to search in a downloaded tiles area as it requires indexing, which is not available for downloaded tiles.

Download can be canceled by calling the `cancelDownload` method of `UnlMapDownloaderService` and the `notifyComplete` callback will be invoked with `ErrorCode.Cancelled`.

> 💡 **Tip**
>
> Trying to download previously downloaded tiles will not result in an error, as downloaded tiles are present inside the application's cache folder as data files.

You can access detailed download statistics for map tiles using the `transferStatistics` property.

> 🚨 **Danger**
>
> Downloaded map tiles via `UnlMapDownloaderService` do not support operations such as free-text search, routing, or turn-by-turn navigation while offline. They are primarily intended for caching map data for visual display purposes only.
>
>For full offline functionality, including search and navigation, refer to the [Manage Offline Content Guide](/01-Overview/04-Todo.md) to learn how to download roadmap data designed for full offline use.

## Change map settings while following the current position[​](#change-map-settings-while-following-the-current-position "Direct link to Change map settings while following the current position")

The `FollowPositionPreferences` class provides more customization while the camera is in the follow position mode. To retrieve an instance, use the snippet below:

* Kotlin
* Java

```kotlin
// Kotlin
val preferences = mapView.preferences?.followPositionPreferences

```

```java
// Java
FollowPositionPreferences preferences = mapView.getPreferences() != null ? mapView.getPreferences().getFollowPositionPreferences() : null;

```

See the [customize follow position settings guide](/05-Positioning%20&%20Sensors/04-Show%20Location%20on%20Map.md#customize-follow-position-settings) for more details.
