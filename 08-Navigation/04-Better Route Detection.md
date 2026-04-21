# Better route detection

The Navigation SDK for Android continuously monitors traffic conditions and automatically evaluates alternative routes to ensure optimal navigation. This feature enhances user experience by providing real-time route adjustments, reducing travel time, and improving overall efficiency, especially in dynamic traffic environments.

## Requirements[​](#requirements "Direct link to Requirements")

#### Route preferences[​](#route-preferences "Direct link to Route preferences")

For this feature to function, the route used in navigation or simulation must be computed with specific settings within the `UnlRoutePreferences` object:

* the `transportMode` needs to be `EUnlRouteTransportMode.Car` or `EUnlRouteTransportMode.Lorry`
* the `avoidTraffic` needs to be `ETrafficAvoidance.All` or `ETrafficAvoidance.Roadblocks`
* the `routeType` needs to be `EUnlRouteType.Fastest`

Unless the settings are set as above the better route detection feature will not trigger.

* Kotlin
* Java

```kotlin
// Kotlin
val routePreferences = UnlRoutePreferences().apply {
    routeType = EUnlRouteType.Fastest
    avoidTraffic = ETrafficAvoidance.Roadblocks
    transportMode = EUnlRouteTransportMode.Car
}

```

```java
// Java
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
routePreferences.setRouteType(EUnlRouteType.Fastest);
routePreferences.setAvoidTraffic(ETrafficAvoidance.Roadblocks);
routePreferences.setTransportMode(EUnlRouteTransportMode.Car);

```

Additional settings can be configured within the `UnlRoutePreferences` object during route calculation, as long as they do not override or conflict with the required preferences mentioned above.

#### Traffic[​](#traffic "Direct link to Traffic")

For the callbacks to be triggered, traffic needs to be present of the route on which the navigation is active.

#### Significant time gain[​](#significant-time-gain "Direct link to Significant time gain")

A newly identified route must have a substantial time delay compared to the current route for it to be considered. The relevant callback will only be triggered if an alternative route offers a time savings of more than five minutes, ensuring that route adjustments are meaningful and beneficial to the user.

> 🚨 **DANGER**
>
> The better route detection feature will not function as intended if any of the required conditions outlined above are not met.

## Listen for notification events[​](#listen-for-notification-events "Direct link to Listen for notification events")

The `startSimulation` and `startNavigation` methods provided by the `UnlNavigationService` class enable the registration of the following callbacks through the `UnlNavigationListener`:

* `onBetterRouteDetected` : Triggered when a better route is identified. It provides information such as the newly detected route, its total travel time, the traffic-induced delay on the new route, and the time savings compared to the current route.
* `onBetterRouteInvalidated` : Triggered when a previously detected better route is no longer valid. This can occur if the user deviates from the shared trunk of both routes, an even better alternative becomes available, or changing traffic conditions eliminate the previously detected advantage.
* `onBetterRouteRejected` : Triggered when no suitable alternative route is found during the better route check.

It is the responsibility of the API user to manage the recommended route. The navigation service does not automatically switch to the better route, requiring explicit handling and implementation by the user.

* Kotlin
* Java

```kotlin
// Kotlin
val navigationListener = UnlNavigationListener.create(
    onBetterRouteDetected = { route, travelTime, delay, timeGain ->
        Log.d("Navigation", "A better route has been detected - total travel time: ${travelTime}s, traffic delay on the better route: ${delay}s, time gain from current route: ${timeGain}s")
        // Do something with the route ...
    },
    onBetterRouteInvalidated = {
        Log.d("Navigation", "The previously found better route is no longer valid")
    },
    onBetterRouteRejected = { error ->
        Log.d("Navigation", "The check for better route failed with reason: $error")
    }
)

navigationService.startSimulation(
    route,
    navigationListener,
    progressListener
)

```

```java
// Java
UnlNavigationListener navigationListener = UnlNavigationListener.create(
    /* onBetterRouteDetected */ (route, travelTime, delay, timeGain) -> {
        Log.d("Navigation", "A better route has been detected - total travel time: " + travelTime + "s, traffic delay on the better route: " + delay + "s, time gain from current route: " + timeGain + "s");
        // Do something with the route ...
    },
    /* onBetterRouteInvalidated */ () -> {
        Log.d("Navigation", "The previously found better route is no longer valid");
    },
    /* onBetterRouteRejected */ (error) -> {
        Log.d("Navigation", "The check for better route failed with reason: " + error);
    }
);

navigationService.startSimulation(
    route,
    navigationListener,
    progressListener
);

```

## Force the check for better route[​](#force-the-check-for-better-route "Direct link to Force the check for better route")

The system automatically performs the better route check at predefined intervals, provided all required conditions are met.

> 📝 **INFO**
>
> In the Android SDK, there is currently no public API to manually trigger a better route check. The system automatically handles route checking at appropriate intervals during navigation or simulation when the required conditions are met.
