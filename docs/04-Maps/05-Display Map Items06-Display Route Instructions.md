# Display route instructions

Instructions are represented as arrows on the map and can be displayed by using `UnlMapView.centerOnRouteInstruction(instruction, zoomLevel, xy, animation, viewAngle)`. To obtain a route's instructions, see the [Route structure](../03-Core/08-Routes.md#route-instruction-structure) section. The following example iterates through all instructions of a route and displays each one by centering:

* Kotlin
* Java

```kotlin
// Kotlin
    val routeInstruction = route.instructions[0]
    mapView.centerOnRouteInstruction(
        instruction = routeInstruction,
        zoomLevel = 75,
        xy = null, // Use default center position
        animation = Animation(EAnimation.Linear, 1000), // 1 second animation
        viewAngle = Double.MAX_VALUE // Use default view angle
    )

```

```java
// Java
    UnlRouteInstruction routeInstruction = route.getInstructions().get(0);
    mapView.centerOnRouteInstruction(
        routeInstruction,
        75,                                          // zoomLevel
        null,                                        // Use default center position
        new Animation(EAnimation.Linear, 1000),      // 1 second animation
        Double.MAX_VALUE                             // Use default view angle
    );

```

![Turn right arrow instruction](../assets/images/example_android_display_route_instruction-cbad713a06ff6b984b9bf0e36b985b49.png "Turn right arrow instruction")

Turn right arrow instruction

The route instruction arrow is automatically cleared when a new route instruction is centered on or when the route is cleared using `mapView.hideRoutes()`.
