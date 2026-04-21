# Overlays

An **Overlay** is an additional map layer in your Virtual Private Map (VPM). Overlays can be default or user-defined. Data is stored on UNL servers, accessible in **online** and **offline** modes, with few exceptions.

In order to define overlay data you can use the UNL Studio. It allows uploading data regarding the POIs and their corresponding categories and binary information (via `GeoJSON` format). An **Overlay** can have multiple associated **categories** and **subcategories**. A single item from an overlay is called an **overlay item**.

> ⚠️ **Warning**
>
> If the corresponding map region has been downloaded locally, overlays will not be available in offline mode unless they are downloaded as well. Refer to the [Downloading overlays](/01-Overview/04-Todo.md) guide for more details.
>
> Most overlay features require a `UnlMap` widget and a style containing the overlay to be applied.

## OverlayCategory[​](#overlaycategory "Direct link to OverlayCategory")

The `OverlayCategory` class represents hierarchical data related to overlay categories.

### OverlayCategory structure[​](#overlaycategory-structure "Direct link to OverlayCategory structure")

| Method               | Description                                   | Type                          |
| -------------------- | --------------------------------------------- | ----------------------------- |
| `uid`                | Unique ID of the category within the overlay. | Int                           |
| `name`               | Name of the category.                         | String?                       |
| `image`              | Image associated with the category.           | `UnlImage?`                      |
| `overlayUid`         | Parent overlay UID.                           | Int                           |
| `hasSubcategories()` | Checks if the category has subcategories.     | Boolean                       |
| `subcategories`      | Gets the subcategories of the category.       | `ArrayList<OverlayCategory>?` |

### Usage[​](#usage "Direct link to Usage")

The `OverlayCategory` class provides the category `uid`, which can be utilized in various search functionalities to filter results. Additionally, the `uid` can be used to filter and manage overlay items that trigger alerts within the `UnlAlarmService`.

## OverlayInfo[​](#overlayinfo "Direct link to OverlayInfo")

`OverlayInfo` is the class that contains information about an overlay.

### OverlayInfo structure[​](#overlayinfo-structure "Direct link to OverlayInfo structure")

| Method     | Description           | Type                          |
| ------------------------ | ----------------------------------------------- | ----------------------------- |
| `uid`                    | Unique identifier for the overlay.              | Int                           |
| `name`                   | Name of the overlay.                            | String?                       |
| `categories`             | List of categories associated with the overlay. | `ArrayList<OverlayCategory>?` |
| `image`                  | UnlImage associated with the overlay.              | `UnlImage?`                      |
| `getCategory(id: Int)`   | Gets the overlay category by its ID.            | `OverlayCategory?`            |
| `hasCategories(id: Int)` | Checks if a category has subcategories.         | Boolean                       |

### Usage[​](#usage-1 "Direct link to Usage")

The primary function of the `OverlayInfo` class is to provide the categories within an overlay. It also provides the `uid` of the overlay which can be used to filter the results of search and also can be used to toggle the visibility of the overlay on the map. Other fields can be displayed on the UI.

## OverlayItem[​](#overlayitem "Direct link to OverlayItem")

An **OverlayItem** represents a single item within an overlay. It contains specific information about that item but also about the overlay it is part of.

### OverlayItem structure[​](#overlayitem-structure "Direct link to OverlayItem structure")

| Method  | Description  | Type  |
| ------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | --------------- |
| `uid`                          | The unique ID of the item within the overlay.  | Int  |
| `categoryId`                    | This item's category ID.  | Int  |
| `image`                         | This item's image.  | `UnlImage?`   |
| `overlayInfo`                   | The parent overlay information.  | `OverlayInfo`  |
| `coordinates`                   | This item's coordinates.  | `UnlCoordinates`  |
| `overlayUid`                    | The parent overlay UID.   | Int   |
| `getPreviewData()`              | Returns a ParameterList with OverlayItem-specific data. | `ParameterList` |
| `getPreviewData(previewFormat: EPreviewDataType)` | Returns a string with OverlayItem-specific data based on the specified preview format.  | String  |
| `getPreviewUrl()`                     | Returns the preview URL, which can be opened in a web browser to present more details about this item.    | String     |
| `hasPreviewExtendedData()`            | Checks if this type of OverlayItem has preview extended data (dynamic data that needs to be downloaded).  | Boolean  |
| `getPreviewExtendedData(paramList: GemList<Parameter>, progressListener: () -> Unit)` | Asynchronously retrieves OverlayItem preview extended data as a parameter list. A progress listener is mandatory. | Unit  |
| `cancelGetPreviewExtendedData()`      | Cancels an asynchronous retrieval of OverlayItem preview extended data.  | Unit   |
| `assign(overlayItem: OverlayItem)`    | Assigns the properties of another OverlayItem to this one.   | Unit   |

### Usage[​](#usage-2 "Direct link to Usage")

`OverlayItems` can be selected from the map or be provided by the `UnlAlarmService` on approach. Other fields and information can be displayed on the UI.

## Classification[​](#classification "Direct link to Classification")

`ECommonOverlayIds` is an enumeration that contains overlay IDs of the predefined overlays. You can access the overlay's ID by using the `value` property.

### ECommonOverlayIds structure[​](#ecommonoverlayids-structure "Direct link to ECommonOverlayIds structure")

| Overlay Name   | Description    |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `Safety`          | Overlay for safety-related information. |
| `SocialLabels`    | Overlay for social labels, including user-generated tags or annotations on the map. **These are yet to be implemented.** |
| `SocialReports`   | Overlay for social reports, such as user-submitted incidents or events.                                                  |
| `PublicTransport` | Overlay for public transport information, including routes, stops, and schedules. |



##### Safety overlay items examples:[​](#safety-overlay-items-examples "Direct link to Safety overlay items examples:")

![Speed Limit Overlay Item](/assets/images/example_speed_limit-a97c79c826062eb46fc71b07ae0c7237.png "Speed limit overlay item")

**Speed limit overlay item**

![Red Light Control Overlay Item](/assets/images/example_red_light_control-1588c5ecc8ee6e30188c85d80439f172.png "Red light control overlay item")

**Red light control overlay item**

##### Public Transport overlay items example:[​](#public-transport-overlay-items-example "Direct link to Public Transport overlay items example:")

![Bus Station Overlay Items](/assets/images/example_bus_station_overlay-68e85e6389e9502288a8f15df1805e98.png "Bus station overlay items")

**Bus station overlay items**

![Train Station Overlay Items](/assets/images/example_train_stations_overlay-85e6798219f58de98b108648cb8c52c3.png "Train station overlay items")

**Train station overlay items**

##### Social reports overlay items examples:[​](#social-reports-overlay-items-examples "Direct link to Social reports overlay items examples:")

![Constructions Overlay Item](/assets/images/example_constructions_overlay-5db7f219b17edd3193e581b8c3bbc38f.png "Constructions overlay item")

**Constructions overlay item**

![Fixed Camera Overlay Item](/assets/images/example_fixed_camera_overlay-c3cadf31ce8682122a5d896c817c944b.png "Fixed camera overlay item")

**Fixed camera overlay item**

## Interaction with overlays[​](#interaction-with-overlays "Direct link to Interaction with overlays")

### OverlayService[​](#overlayservice "Direct link to OverlayService")

The `OverlayService` is a key component for managing overlays in the Navigation SDK. It provides methods to retrieve, enable, disable, and manage overlay data, both online and offline.

#### Retrieving overlay info with OverlayService[​](#retrieving-overlay-info-with-overlayservice "Direct link to Retrieving overlay info with OverlayService")

You can retrieve the list of all available overlays for the current map style using the `getAvailableOverlays(onCompleted: (errorCode: Int, hint: String?) -> Unit)` method from the `OverlayService`. This method returns a `Pair<ArrayList<OverlayInfo>?, Boolean>?`. The list of `OverlayInfo` objects represents the available overlays, and the `Boolean` indicates that some information is not available and will be downloaded when network is available. If not all overlay information is available onboard, a notification will be sent when it will be downloaded via a call to the lambda provided as a parameter to the `getAvailableOverlays()` method.

* Kotlin
* Java

```kotlin
// Kotlin
val (availableOverlaysList, isDataAvailable) = service.getAvailableOverlays(onCompleted = { errorCode, hint ->
    if (UnlError.isError(errorCode)) {
        // handle error
    } else {
        //handle data
    }
}) ?: Pair(null, null)
if (isDataAvailable == null) {
    //handle unexpected error
}

```

```java
// Java
Pair<ArrayList<OverlayInfo>, Boolean> result = service.getAvailableOverlays((errorCode, hint) -> {
    if (UnlError.isError(errorCode)) {
        // handle error
    } else {
        //handle data
    }
});
ArrayList<OverlayInfo> availableOverlaysList = result != null ? result.first : null;
Boolean isDataAvailable = result != null ? result.second : null;
if (isDataAvailable == null) {
    //handle unexpected error
}

```

To get overlay info for a specific overlay you can use the `getOverlayInfo(overlayUid: ECommonOverlayId, onCompleted: (errorCode: Int, hint: String?) -> Unit)` method which returns a `Pair<OverlayInfo?, Boolean>` object. The `Boolean` indicates that some information is not available and will be downloaded when network is available. If not all overlays info is available onboard a notification will be sent when it will be downloaded via a call to the lambda provided as a parameter to the `getOverlayInfo()` method.

* Kotlin
* Java

```kotlin
// Kotlin
val (overlayInfo, isDataAvailable) = service.getOverlayInfo(overlayUid, onCompleted = { errorCode, hint ->
    if (UnlError.isError(errorCode)) {
        // handle error
    } else {
        //handle data
    }
}) ?: Pair(null, null)
if (isDataAvailable == null) {
    //handle unexpected error
}

```

```java
// Java
Pair<OverlayInfo, Boolean> result = service.getOverlayInfo(overlayUid, (errorCode, hint) -> {
    if (UnlError.isError(errorCode)) {
        // handle error
    } else {
        //handle data
    }
});
OverlayInfo overlayInfo = result != null ? result.first : null;
Boolean isDataAvailable = result != null ? result.second : null;
if (isDataAvailable == null) {
    //handle unexpected error
}

```

To get overlay info for a group of specific overlays you can use the `getOverlaysInfo( ArrayList<ECommonOverlayId>, onCompleted: (errorCode: Int, hint: String?) -> Unit)` method which returns a `Pair<OverlayInfo?, Boolean>` object. The `Boolean` indicates that some information is not available and will be downloaded when network is available. If not all overlays info is available onboard a notification will be sent when it will be downloaded via a call to the lambda provided as a parameter to the `getOverlayInfo()` method.

* Kotlin
* Java

```kotlin
// Kotlin
val overlayUids = arrayListOf(ECommonOverlayId.Safety, ECommonOverlayId.PublicTransport)
val (overlaysInfo, isDataAvailable) = service.getOverlaysInfo(overlayUids, onCompleted = { errorCode, hint ->
    if (UnlError.isError(errorCode)) {
        // handle error
    } else {
        //handle data
    }
}) ?: Pair(null, null)
if (isDataAvailable == null) {
    //handle unexpected error
}

```

```java
// Java
ArrayList<ECommonOverlayId> overlayUids = new ArrayList<>();
overlayUids.add(ECommonOverlayId.Safety);
overlayUids.add(ECommonOverlayId.PublicTransport);
Pair<ArrayList<OverlayInfo>, Boolean> result = service.getOverlaysInfo(overlayUids, (errorCode, hint) -> {
    if (UnlError.isError(errorCode)) {
        // handle error
    } else {
        //handle data
    }
});
ArrayList<OverlayInfo> overlaysInfo = result != null ? result.first : null;
Boolean isDataAvailable = result != null ? result.second : null;
if (isDataAvailable == null) {
    //handle unexpected error
}

```

#### Enabling and disabling overlays[​](#enabling-and-disabling-overlays "Direct link to Enabling and disabling overlays")

You can enable or disable overlays on the map using the `enableOverlay(overlayUid: Int, categoryID: Int)` method and `disableOverlay(overlayUid: Int, categoryID: Int)` from the `OverlayService`. Check whether an overlay is enabled or disabled using the `isOverlayEnabled(overlayUid: Int, categoryID: Int)` method.

* Kotlin
* Java

```kotlin
// Kotlin
val service = UnlSdk.getOverlayService()
val overlayUid = ECommonOverlayId.Safety.value
val categoryID = -1  // Use -1 to refer to the entire overlay, a specific category ID to refer to a category within the overlay
// Enable an overlay
val errorCodeWhileEnabling = service.enableOverlay(overlayUid, categoryID)
// Disable an overlay
val errorCodeWhileDisabling = service.disableOverlay(overlayUid, categoryID)
// Check if an overlay is enabled
val isEnabled = service.isOverlayEnabled(overlayUid, categoryID)

```

```java
// Java
OverlayService service = UnlSdk.getOverlayService();
int overlayUid = ECommonOverlayId.Safety.getValue();
int categoryID = -1; // Use -1 to refer to the entire overlay, a specific category ID to refer to a category within the overlay
// Enable an overlay
int errorCodeWhileEnabling = service.enableOverlay(overlayUid, categoryID);
// Disable an overlay
int errorCodeWhileDisabling = service.disableOverlay(overlayUid, categoryID);
// Check if an overlay is enabled
boolean isEnabled = service.isOverlayEnabled(overlayUid, categoryID);

```

#### Retrieving overlay data offline[​](#retrieving-overlay-data-offline "Direct link to Retrieving overlay data offline")

The **offline data grabber** downloads an overlay covering dataset for every new downloaded road map content. The offline data is automatically grabbed immediately after a road map content download finishes and is stored in the SDK's permanent cache. To enable or disable the overlay data grabber, use the following methods from the `OverlayService`: `enableOverlayOfflineDataGrabber(overlayUid: Int)` and `disableOverlayOfflineDataGrabber(overlayUid: Int)`. You can check if the offline data grabber is enabled using the `isOfflineDataGrabberEnabled(overlayUid: Int)` method.

* Kotlin
* Java

```kotlin
// Kotlin
val service = OverlayService()
val isEnabled =  service.isOverlayOfflineDataGrabberEnabled(overlayId)
val errorCodeWhileEnabling = service.enableOverlayOfflineDataGrabber(overlayId)
val errorCodeWhileDisabling = service.disableOverlayOfflineDataGrabber(overlayId)

```

```java
// Java
OverlayService service = new OverlayService();
boolean isEnabled = service.isOverlayOfflineDataGrabberEnabled(overlayId);
int errorCodeWhileEnabling = service.enableOverlayOfflineDataGrabber(overlayId);
int errorCodeWhileDisabling = service.disableOverlayOfflineDataGrabber(overlayId);

```

> 🚨 **Danger**
>
> Avoid confusing the uid of `OverlayInfo`, `OverlayCategory`, and `OverlayItem`, as they each serve distinct purposes.

### Selecting overlay items[​](#selecting-overlay-items "Direct link to Selecting overlay items")

Overlay items are selectable. When a user taps or clicks, you can identify specific overlay items programmatically (e.g., through the function `cursorSelectionOverlayItems()`). Please refer to the [Map Selection Functionality](/docs/android/guides/maps/interact-with-map.md#map-selection-functionality) guide for more details.

### Searching overlay items[​](#searching-overlay-items "Direct link to Searching overlay items")

Overlays are searchable in multiple ways, typically by setting the appropriate properties in the search preferences during a regular search. More details can be found within the [Get started with Search](/docs/android/guides/search/get-started-search.md) guide.

### Calculating route with overlay items[​](#calculating-route-with-overlay-items "Direct link to Calculating route with overlay items")

Overlay items are **not** designed for route calculation and navigation.

> 💡 **Tip**
>
> Create a new landmark using the overlay item's relevant coordinates and a representative name, then utilize this object for routing purposes.

### Displaying overlay item information[​](#displaying-overlay-item-information "Direct link to Displaying overlay item information")

Overlay items can contain additional information that can be displayed to the user. This information can be accessed using the `getPreviewData(previewFormat: EPreviewDataType)` or `getPreviewData()` and `getPreviewUrl()` methods from the `OverlayItem` class. The `getPreviewData(previewFormat: EPreviewDataType)` method returns a string with overlay item-specific data based on the specified preview format, while `getPreviewData()` gets the same data as a `ParameterList` .The `getPreviewUrl()` method returns a URL that can be opened in a web browser to present more details about the item.

Here is an example of how to display overlay item information:

* Kotlin
* Java

```kotlin
// Kotlin
overlayItem.getPreviewData()?.let { parameters ->
  var imageBitmap = ImageBitmap(1, 1)

  overlayItem.image?.let { image ->
      GemUtilImages.asBitmap(
          img = image,
          width = (overlayImageSize * (image.aspectRatio!!.width / image.aspectRatio!!.height)).toInt(),
          height = overlayImageSize
      )?.let { bmp ->
          imageBitmap = bmp.asImageBitmap()
      }
  }
  for (parameter in parameters) {
      when (parameter.name) {
          "speedValue" -> titleText = parameter.value as String
          "speedUnit" -> descriptionText = parameter.value as String
          // other parameters...
      }
  }
}


```

```java
// Java
ParameterList parameters = overlayItem.getPreviewData();
if (parameters != null) {
    ImageBitmap imageBitmap = new ImageBitmap(1, 1);

    UnlImage image = overlayItem.getImage();
    if (image != null) {
        Bitmap bmp = GemUtilImages.asBitmap(
            image,
            (int) (overlayImageSize * (image.getAspectRatio().getWidth() / image.getAspectRatio().getHeight())),
            overlayImageSize
        );
        if (bmp != null) {
            imageBitmap = bmp; // Convert as needed for your UI framework
        }
    }
    for (Parameter parameter : parameters) {
        switch (parameter.getName()) {
            case "speedValue":
                titleText = (String) parameter.getValue();
                break;
            case "speedUnit":
                descriptionText = (String) parameter.getValue();
                break;
            // other parameters...
        }
    }
}

```

> 🚨 **Danger**
>
> The `previewData` returned is not available if the parent map tile is disposed. Please get the preview data before further interactions with the map.

### Get notifications when approaching overlay items[​](#get-notifications-when-approaching-overlay-items "Direct link to Get notifications when approaching overlay items")

Alarms can be configured to notify users when they approach specific overlay items from selected overlays. See the [Landmarks and overlay alarms](/01-Overview/04-Todo.md) guide for more details about implementing this feature.

### Highlight overlay items on the map[​](#highlight-overlay-items-on-the-map "Direct link to Highlight overlay items on the map")

You can't directly highlight overlay items on the map, but you can create a `UnlLandmark` filled with relevant information and an image to represent the overlay item on the map.

* Kotlin
* Java

```kotlin
// Kotlin
 fun highlightPlace(coordinates: UnlCoordinates, image: UnlImage, mapView: UnlMapView) {
    val landmark = UnlLandmark()
    landmark.coordinates = coordinates
    landmark.image = image

    mapView.centerOnCoordinates(coordinates)

    val displaySettings = HighlightRenderSettings()
    displaySettings.setOptions(EHighlightOptions.ShowLandmark.value or EHighlightOptions.Overlap.value)

    mapView.activateHighlightLandmarks(landmark, displaySettings)
 }

```

```java
// Java
void highlightPlace(UnlCoordinates coordinates, UnlImage image, UnlMapView mapView) {
    UnlLandmark landmark = new UnlLandmark();
    landmark.setCoordinates(coordinates);
    landmark.setImage(image);

    mapView.centerOnCoordinates(coordinates);

    HighlightRenderSettings displaySettings = new HighlightRenderSettings();
    displaySettings.setOptions(EHighlightOptions.ShowLandmark.getValue() | EHighlightOptions.Overlap.getValue());

    mapView.activateHighlightLandmarks(landmark, displaySettings);
}

```
