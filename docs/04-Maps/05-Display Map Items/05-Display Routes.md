# Display routes

Routes can be displayed on the map by using `mapView.preferences?.routes?.add(route, isMainRoute)`. Multiple routes can be displayed at the same time, but only one is the main one, the others being treated as secondary. Specifying which one is the main route can be done when calling `MapViewRoutesCollection.add()` by passing `true` to the `mainRoute` parameter, or by calling the `MapViewRoutesCollection.mainRoute` setter.

* Kotlin
* Java

```kotlin
// Kotlin
mapView.preferences?.routes?.add(route, true)
mapView.centerOnRoute(route)

```

```java
// Java
if (mapView.getPreferences() != null) {
    mapView.getPreferences().getRoutes().add(route, true);
}
mapView.centerOnRoute(route);

```

![Route displayed](/assets/images/example_android_display_routes1-34c95d1808aec210f42ad26316ac1f67.png "Route displayed")

**Route displayed**

> 💡 **Tip**
>
> To center on a route with padding, refer to the [Adjust Map View](/04-Maps/03-Adjust%20the%20Map%20View.md#map-centering-on-area-with-padding) guide. Utilize the `rect` parameter in the `centerOnRoute` method to define the specific region of the viewport that should be centered.

* Kotlin
* Java

```kotlin
// Kotlin
mapView.preferences?.routes?.add(route1, true)
mapView.preferences?.routes?.add(route2, false)
mapView.preferences?.routes?.add(route3, false)
mapView.centerOnRoutes(arrayListOf(route1, route2, route3))

```

```java
// Java
if (mapView.getPreferences() != null) {
    mapView.getPreferences().getRoutes().add(route1, true);
    mapView.getPreferences().getRoutes().add(route2, false);
    mapView.getPreferences().getRoutes().add(route3, false);
}
ArrayList<UnlRoute> routes = new ArrayList<>();
routes.add(route1);
routes.add(route2);
routes.add(route3);
mapView.centerOnRoutes(routes);

```

![Three routes displayed](/assets/images/example_android_display_routes2-e3a8baf962dfaff34b34e8492767dda7.png "Three routes displayed, one in the middle is main")

**Three routes displayed, one in the middle is main**

Route appearance on map can be customized via `UnlRouteRenderSettings` when added, passed to the `MapViewRoutesCollection.addWithRenderSettings()` method, or later on, via `UnlMapViewRoute.renderSettings` setter.

* Kotlin
* Java

```kotlin
// Kotlin
val renderSettings = UnlRouteRenderSettings().apply {
    innerColor = Rgba(255, 0, 0, 255)
}
mapView.preferences?.routes?.addWithRenderSettings(route, renderSettings)

```

```java
// Java
UnlRouteRenderSettings renderSettings = new UnlRouteRenderSettings();
renderSettings.setInnerColor(new Rgba(255, 0, 0, 255));
if (mapView.getPreferences() != null) {
    mapView.getPreferences().getRoutes().addWithRenderSettings(route, renderSettings);
}

```

* Kotlin
* Java

```kotlin
// Kotlin
val mapViewRoute = mapView.preferences?.routes?.getMapViewRouteByIndex(0)
mapViewRoute?.renderSettings = UnlRouteRenderSettings().apply {
    innerColor = Rgba(255, 0, 0, 255)
}

```

```java
// Java
UnlMapViewRoute mapViewRoute = null;
if (mapView.getPreferences() != null) {
    mapViewRoute = mapView.getPreferences().getRoutes().getMapViewRouteByIndex(0);
}
if (mapViewRoute != null) {
    UnlRouteRenderSettings renderSettings = new UnlRouteRenderSettings();
    renderSettings.setInnerColor(new Rgba(255, 0, 0, 255));
    mapViewRoute.setRenderSettings(renderSettings);
}

```

All dimensional sizes within the `UnlRouteRenderSettings` are measured in millimeters.

![Route with custom render](/assets/images/example_android_display_routes3-00b0a6b388e968aa4118c093540eb04a.png "Route displayed with custom render settings")

**Route displayed with custom render settings**

### Convenience methods[​](#convenience-methods "Direct link to Convenience methods")

The `UnlMapView` class provides convenient `presentRoute` and `presentRoutes` methods that combine adding routes to the collection and centering the map:

* Kotlin
* Java

```kotlin
// Kotlin
// Present a single route with default settings
mapView.presentRoute(route)

// Present a single route with custom settings
mapView.presentRoute(
    route,
    displayBubble = true,
    displayRouteName = true,
    displayTrafficIcon = true,
    centerMapView = true
)

// Present multiple routes
mapView.presentRoutes(
    routes,
    mainRoute = routes.first(),
    displayBubble = true,
    centerMapView = true
)

```

```java
// Java
// Present a single route with default settings
mapView.presentRoute(route);

// Present a single route with custom settings
mapView.presentRoute(
    route,
    true,  // displayBubble
    true,  // displayRouteName
    true,  // displayTrafficIcon
    true   // centerMapView
);

// Present multiple routes
mapView.presentRoutes(
    routes,
    routes.get(0), // mainRoute
    true,           // displayBubble
    true            // centerMapView
);

```

To remove displayed routes, use `MapViewRoutesCollection.clear()`. You can also remove individual routes with `mapView.preferences?.routes?.remove(route)`.

### Set route labels[​](#set-route-labels "Direct link to Set route labels")

A route can include a label that provides information such as ETA, distance, toll prices, and more. To attach a label to a route, use the `addWithAttributes` method of the `MapViewRoutesCollection`:

* Kotlin
* Java

```kotlin
// Kotlin
mapView.preferences?.routes?.addWithAttributes(route, true, "Added label")

```

```java
// Java
if (mapView.getPreferences() != null) {
    mapView.getPreferences().getRoutes().addWithAttributes(route, true, "Added label");
}

```

![Route with label](/assets/images/example_android_route_label1-dae1f891a5944b71918a083ce9ff5cf1.png "Route with label")

**Route with label**

You can enhance the label by adding up to **two icons** using the `images` parameter, which accepts an `UnlImageList`. Available icons can be accessed through the `UnlImageDatabase` class.

* Kotlin
* Java

```kotlin
// Kotlin
val images = UnlImageList().apply {
    UnlImageDatabase.tollImage?.let { add(it) }
    UnlImageDatabase.ferryImage?.let { add(it) }
}

mapView.preferences?.routes?.addWithAttributes(
    route, true,
    "This is a custom label",
    images
)

```

```java
// Java
UnlImageList images = new UnlImageList();
if (UnlImageDatabase.getTollImage() != null) {
    images.add(UnlImageDatabase.getTollImage());
}
if (UnlImageDatabase.getFerryImage() != null) {
    images.add(UnlImageDatabase.getFerryImage());
}

if (mapView.getPreferences() != null) {
    mapView.getPreferences().getRoutes().addWithAttributes(
        route, true,
        "This is a custom label",
        images
    );
}

```

![Label with custom icons](/assets/images/example_android_custom_label-29dcfb5bcd11419df53ec869661fa601.png "Label with custom icons")

**Label with custom icons**

The label can also be auto-generated using route information:

* Kotlin
* Java

```kotlin
// Kotlin
// Auto-generated labels are created using route's timeDistance information
val route = routes.first()
val timeDistance = route.timeDistance!!
val distText = GemUtil.getDistText(timeDistance.totalDistance, UnlSdkSettings.unitSystem, true)
val timeText = GemUtil.getTimeText(timeDistance.totalTime)
val autoLabel = "${distText.first} ${distText.second}\n${timeText.first} ${timeText.second}"

mapView.preferences?.routes?.addWithAttributes(route, true, autoLabel)

```

```java
// Java
// Auto-generated labels are created using route's timeDistance information
UnlRoute route = routes.get(0);
UnlTimeDistance timeDistance = route.getTimeDistance();
Pair<String, String> distText = GemUtil.getDistText(timeDistance.getTotalDistance(), UnlSdkSettings.getUnitSystem(), true);
Pair<String, String> timeText = GemUtil.getTimeText(timeDistance.getTotalTime());
String autoLabel = distText.getFirst() + " " + distText.getSecond() + "\n" + timeText.getFirst() + " " + timeText.getSecond();

if (mapView.getPreferences() != null) {
    mapView.getPreferences().getRoutes().addWithAttributes(route, true, autoLabel);
}

```

![Route with generated label](/assets/images/example_android_route_label2-a6681a848d0ff0ac5b76af93a2e6c114.png "Route with generated label")

**Route with generated label**

The label of a route added to the collection can be hidden by calling `MapViewRoutesCollection.hideLabel(route)`. Labels can also be managed through a `UnlMapViewRoute` object. The `label` property is used to assign a label, while the `hideLabel()` method can be used to hide it.

> 🚨 **Danger**
>
> Auto-generated labels will override any customizations made with the `label` and `images` parameters.

### Check what portion of a route is visible on a screen region[​](#check-what-portion-of-a-route-is-visible-on-a-screen-region "Direct link to Check what portion of a route is visible on a screen region")

To retrieve the visible portion of a route - defined by its start and end distances in meters - use the `getVisibleRouteInterval` method from the `UnlMapView`:

* Kotlin
* Java

```kotlin
// Kotlin
val visibleInterval = mapView.getVisibleRouteInterval(route, null)
val startRouteVisibleIntervalMeters = visibleInterval?.x?.toInt() ?: 0
val endRouteVisibleIntervalMeters = visibleInterval?.y?.toInt() ?: 0

```

```java
// Java
PointF visibleInterval = mapView.getVisibleRouteInterval(route, null);
int startRouteVisibleIntervalMeters = visibleInterval != null ? (int) visibleInterval.x : 0;
int endRouteVisibleIntervalMeters = visibleInterval != null ? (int) visibleInterval.y : 0;

```

You can also provide a custom screen region to the `getVisibleRouteInterval` method by passing a `UnlRect` parameter, instead of using the entire viewport. The method will return `null` if the route is not visible on the provided viewport/region of the viewport
