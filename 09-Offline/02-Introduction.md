# Introduction

The Navigation SDK for Android provides extensive offline functionality through its map download capabilities. Users can search for landmarks, calculate and navigate routes, and explore the map without requiring an active internet connection.

However, certain features, such as overlays, live traffic information and other online-dependent services, are unavailable in offline mode.

The SDK empowers users to download maps for entire countries or specific regions directly to their devices, enabling seamless offline access to essential features. Additionally, it allows users to update their downloaded maps, ensuring they always have access to the latest data and improvements for offline use.

The SDK provides automatic update capabilities for map data. New map versions are typically released every few weeks globally, providing regular enhancements and improvements.

Additionally, the SDK notifies developers about available updates, keeping them informed and in control. Developers can configure update preferences, such as restricting updates or downloads to Wi-Fi connections only, or permitting them over cellular data, offering flexibility based on user needs and network conditions.

## SDK Features available offline[​](#sdk-features-available-offline "Direct link to SDK Features available offline")

### Core Entities[​](#core-entities "Direct link to Core Entities")

| Entity                           | Offline Availability                     |
| ------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Base entities](/03-Core/02-Base%20Entities.md)      | Fully operational in offline use cases, as they do not require an active internet connection.                                                                                 |
| [Position](/03-Core/03-Positions.md)                 | Partially available. Raw position data is always accessible, but map-matched position data is only available if the relevant region has been previously downloaded or cached. |
| [Landmarks](/03-Core/04-Landmarks.md)                | Fully available in offline mode.                                                                                                                                              |
| [Markers](/03-Core/05-Markers.md)                    | Fully available in offline mode.                                                                                                                                              |
| [Overlays](/03-Core/06-Overlays.md)                  | Not available in offline mode.                                                                                                                                                |
| [Routes](/03-Core/08-Routes.md)                      | Partially available. The `trafficEvents` getter will return an empty list when there is no internet connection.                                                               |
| [Navigation](/03-Core/09-Navigation%20Instructions.md) | Fully available in offline mode if navigation is started on an offline-calculated route.                                                                                      |

> 📝 **INFO**
>
> Map tiles are automatically cached based on the user's location, camera position, and calculated routes to enhance performance and offline accessibility.

### UnlMapView[​](#mapview "Direct link to UnlMapView")

The `UnlMapView` methods function as expected in offline mode. However, methods that request data from regions that are not covered or cached will return empty results (e.g., an empty list or null, depending on the method). For instance, calling `UnlMapView` methods with coordinates outside of covered areas will result in an empty list or null.

### Map Styling[​](#map-styling "Direct link to Map Styling")

Setting a new map style is supported in offline mode, provided the style has been downloaded beforehand. Note that styles containing extensive data, such as Satellite and Weather styles, may not display meaningful information when offline.

### Services[​](#services "Direct link to Services")

The following services are available offline only within downloaded map regions:

* `UnlRoutingService`
* `UnlSearchService`
* `GuidedAddressSearchService`
* `NavigationService`
* `UnlLandmarkStoreService`
* `UnlPositionService`

The remaining services such as `OverlayService` and `ContentStore` are **not supported** in offline mode.

Calling `UnlSearchService.search` with queries outside of downloaded map regions will result in an error. Same goes for `UnlRoutingService.calculateRoute`. Other services might return connection-related errors.

> 📝 **INFO**
>
> `UnlAlarmService` has limited functionality during offline sessions, as overlay-related features are unavailable.

### SDK Settings[​](#sdk-settings "Direct link to SDK Settings")

Most methods in `UnlSdkSettings` function independently of the internet connection status, with the exception of authorization-related functionalities. Authorization with the Service Key **requires** an active internet connection. Consequently, you cannot authorize the Navigation SDK for Android without being online.
