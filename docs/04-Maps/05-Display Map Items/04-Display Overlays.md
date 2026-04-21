# Display overlays

Overlays are used to provide enhanced, layered information that adds contextual value to a base map, offering users dynamic, interactive, and customized data that can be visualized on top of other map elements.

### Disabling overlays[​](#disabling-overlays "Direct link to Disabling overlays")

Overlays can be disabled either by applying a predefined, custom map style created in UNL Studio - where certain overlays are already disabled - or by using the `disableOverlay` method, as shown below:

* Kotlin
* Java

```kotlin
// Kotlin
val overlayService = OverlayService()
val error = overlayService.disableOverlay(ECommonOverlayId.PublicTransport.value, categoryId = -1)

```

```java
// Java
OverlayService overlayService = new OverlayService();
int error = overlayService.disableOverlay(ECommonOverlayId.PublicTransport.getValue(), -1);

```

Passing -1 (default value) as the optional `categoryId` parameter indicates that we want to disable the entire public transport overlay, rather than targeting a specific category.

The error returned will be `success` if the overlay was disabled or `notFound` if no overlay (or overlay category) with the specified ID was found in the applied style.

To disable specific overlays within a category, you'll need to retrieve their unique identifiers (uid) as shown below:

* Kotlin
* Java

```kotlin
// Kotlin
val overlayService = OverlayService()

// Get available overlays with callback
val overlayCollection = overlayService.getAvailableOverlays { error, _ ->
    if (error == UnlError.NoError) {
        // Access overlays through UnlMapView preferences (if available)
    }
}
overlayCollection?.first?.let { collection ->
    if (collection.size > 0) {
        val overlayInfo = collection[0]
        val uid = overlayInfo.uid
        val disableError = overlayService.disableOverlay(uid, -1)
        // Handle the error result
        Log.d("Overlay", "Disable result: $disableError")
    }
}

```

```java
// Java
OverlayService overlayService = new OverlayService();

// Get available overlays with callback
Pair<List<OverlayInfo>, Integer> overlayCollection = overlayService.getAvailableOverlays((error, result) -> {
    if (error == UnlError.NoError) {
        // Access overlays through UnlMapView preferences (if available)
    }
});
if (overlayCollection != null && overlayCollection.getFirst() != null) {
    List<OverlayInfo> collection = overlayCollection.getFirst();
    if (collection.size() > 0) {
        OverlayInfo overlayInfo = collection.get(0);
        int uid = overlayInfo.getUid();
        int disableError = overlayService.disableOverlay(uid, -1);
        // Handle the error result
        Log.d("Overlay", "Disable result: " + disableError);
    }
}

```

### Enabling overlays[​](#enabling-overlays "Direct link to Enabling overlays")

In a similar way, the overlay can be enabled using the `enableOverlay` method by providing the overlay id. It also has an optional `categoryId` parameter, which when left as default, it activates whole overlay rather than a specific category.

* Kotlin
* Java

```kotlin
// Kotlin
val overlayService = OverlayService()
val error = overlayService.enableOverlay(ECommonOverlayId.PublicTransport.value, categoryId = -1)

```

```java
// Java
OverlayService overlayService = new OverlayService();
int error = overlayService.enableOverlay(ECommonOverlayId.PublicTransport.getValue(), -1);

```
