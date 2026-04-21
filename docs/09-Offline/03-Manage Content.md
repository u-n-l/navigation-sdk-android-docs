# Manage Content

The Navigation SDK for Android offers comprehensive functionality for managing offline content.

The supported downloadable content types are defined in the `EContentType` enum:

* `ViewStyleHighRes`: High-dpi screen optimized map styles that can be applied offline. These include both a selection of default styles and user-created styles from the studio, based on the API key.
* `ViewStyleLowRes`: Low-dpi screen map styles, optimized for smaller file sizes while maintaining essential details.
* `RoadMap`: Offline maps covering countries and regions. Within a downloaded region, users can perform searches, calculate routes, and navigate without an internet connection.
* `HumanVoice`: Pre-recorded voice files used to deliver spoken navigation instructions and warnings.

> 💡 **TIP**
>
> For most use cases, the high-resolution map styles option is recommended over its low-resolution counterpart.

The `ContentStore` class is responsible for managing and providing a list of downloadable items. Each item is represented by the `ContentStoreItem` class, which encapsulates details such as name, image, type, version, and size. Additionally, it offers operations for downloading and deleting content.

> 🚨 **DANGER**
>
> Ensure that the Service Key is both set and valid. Some operations might fail if no valid Service Key is set.

> 🚨 **DANGER**
>
> Modifying downloaded maps (download, delete, update) may interrupt ongoing operations such as search, route calculation, or navigation.

## List Online Content[​](#list-online-content "Direct link to List Online Content")

To retrieve a list of available content from the UNL servers, use the `asyncGetStoreContentList` method from the `ContentStore` class. This method returns an `ErrorCode` and a `UnlProgressListener` for tracking the operation. The operation can be canceled using the returned `UnlProgressListener`.

The method accepts the content type as an argument and provides a callback with:

* The operation error code
* A list of `ContentStoreItem` objects

- Kotlin
- Java

```kotlin
// Kotlin
val (errorCode, progressListener) = ContentStore().asyncGetStoreContentList(
    EContentType.RoadMap,
    onStarted = {
        // Handle operation start
    },
    onProgress = {
        // Handle progress updates
    },
    onCompleted = { resultList, errorCode, hint ->
        if (errorCode == UnlError.NoError) {
            // Use the resultList of ContentStoreItem objects
        } else {
            Log.e("ContentStore", "Failed to get content list: ${UnlError.getMessage(errorCode)}")
        }
    }
)

```

```java
// Java
Pair<Integer, UnlProgressListener> result = new ContentStore().asyncGetStoreContentList(
    EContentType.RoadMap,
    () -> {
        // Handle operation start
    },
    (progress) -> {
        // Handle progress updates
    },
    (resultList, errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            // Use the resultList of ContentStoreItem objects
        } else {
            Log.e("ContentStore", "Failed to get content list: " + UnlError.getMessage(errorCode));
        }
    }
);
int errorCode = result.first;
UnlProgressListener progressListener = result.second;

```

> 📝 **INFO**
>
> The `asyncGetStoreContentList` method should be called only when an active internet connection is available and the current offline version is not expired. If no internet connection is available or if the current offline map version is expired, use the `getLocalContentList` method to retrieve the offline content list instead.

> 🚨 **DANGER**
>
> Do not invoke `asyncGetStoreContentList` before the map data is ready. Wait until the SDK has initialized the map properly before calling it.

## List Local Content[​](#list-local-content "Direct link to List Local Content")

The `getLocalContentList` method can be used to get the list of local content available offline.

* Kotlin
* Java

```kotlin
// Kotlin
val contentStore = ContentStore()
val items = contentStore.getLocalContentList(EContentType.RoadMap)
if (items != null) {
    // Do something with the items
    for (item in items) {
        Log.d("ContentStore", "Item: ${item.name}")
    }
}

```

```java
// Java
ContentStore contentStore = new ContentStore();
ContentStoreItemList items = contentStore.getLocalContentList(EContentType.RoadMap);
if (items != null) {
    // Do something with the items
    for (ContentStoreItem item : items) {
        Log.d("ContentStore", "Item: " + item.getName());
    }
}

```

> 📝 **INFO**
>
> The `getLocalContentList` method returns content store items that are either ready for use, currently downloading, or pending download.

## Filter Content[​](#filter-content "Direct link to Filter Content")

To obtain a **filtered** list of available content from the UNL servers, use the `asyncGetStoreFilteredList` method from the `ContentStore` class. You can filter content by specifying country ISO 3166-1 alpha-3 codes and by geographic area.

* Kotlin
* Java

```kotlin
// Kotlin
val countries = arrayListOf("USA", "CAN")
val area = RectangleGeographicArea(
    topLeft = UnlCoordinates(latitude = 53.7731, longitude = -1.7990),
    bottomRight = UnlCoordinates(latitude = 38.4549, longitude = 21.1696)
)

val (errorCode, progressListener) = ContentStore().asyncGetStoreFilteredList(
    EContentType.RoadMap,
    countries,
    area,
    onCompleted = { resultList, errorCode, hint ->
        if (errorCode == UnlError.NoError) {
            // Use the filtered resultList
        }
    }
)

```

```java
// Java
ArrayList<String> countries = new ArrayList<>(Arrays.asList("USA", "CAN"));
RectangleGeographicArea area = new RectangleGeographicArea(
    new UnlCoordinates(53.7731, -1.7990),
    new UnlCoordinates(38.4549, 21.1696)
);

Pair<Integer, UnlProgressListener> result = new ContentStore().asyncGetStoreFilteredList(
    EContentType.RoadMap,
    countries,
    area,
    (resultList, errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            // Use the filtered resultList
        }
    }
);
int errorCode = result.first;
UnlProgressListener progressListener = result.second;

```

> 📝 **INFO**
>
> The filtered content should be requested via a call to `asyncGetStoreFilteredList` method. Use the `storeFilteredList` property to retrieve previously filtered results.

### Method behaviour[​](#method-behaviour "Direct link to Method behaviour")

|Condition           | `onComplete` Result                              |
| ------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| The `countries` list contains invalid ISO 3166-1 alpha-3 codes                                    | `UnlError.NoError` with an empty `ContentStoreItem` list.                                       |
| The `countries` list includes countries incompatible with the specified `RectangleGeographicArea` | `UnlError.NoError` with an empty `ContentStoreItem` list.                                       |
| Insufficient memory to complete the operation                                                     | `UnlError.NoMemory` with an empty `ContentStoreItem` list.                                      |
| Invalid `UnlGeographicArea` (e.g., invalid coordinates)                                              | `UnlError.NoError` with a full list of `ContentStoreItem`; behaves as if no filter was applied. |
| The `area` parameter is an empty `UnlGeographicArea`                                                 | `UnlError.InvalidInput` with an empty `ContentStoreItem` list.                                  |
| HTTP request failed                                                                               | `UnlError.General` with an empty `ContentStoreItem` list.                                       |

## Content Store Item Fields[​](#content-store-item-fields "Direct link to Content Store Item Fields")

#### Fields Containing General Information[​](#fields-containing-general-information "Direct link to Fields Containing General Information")

| Field Name          | Description                                                      |
| ------------------- | ---------------------------------------------------------------- |
| `name`              | The name of the associated product, automatically translated.    |
| `id`                | The unique ID of the item in the content store.                  |
| `type`              | The type of the product as an `EContentType` value.              |
| `filename`          | The full path to the content data file.                          |
| `totalSize`         | The total size of the content in bytes.                          |
| `availableSize`     | The downloaded size of the content.                              |
| `updateSize`        | The update size if an update is available.                       |
| `status`            | The current status of the item as an `EContentStoreItemStatus`.  |
| `contentParameters` | Additional information about an item as a `ParameterList` object |
| `imagePreview`      | The image associated with the content store item if available    |

> 💡 **TIP**
>
> For checking if a `ContentStoreItem` is downloaded/available/downloading/updating use the `status` field value:
>
>* `Unavailable`: The content store item is not downloaded and cannot be used.
>* `Completed`: The content store item has been downloaded and is ready to be used.
>* `Paused`: The download operation has been paused by the user.
>* `DownloadQueued`: The download is queued and will proceed once resources are available.
>* `DownloadWaiting`: Waiting for a connection or other condition to proceed.
>* `DownloadWaitingFreeNetwork`: The SDK is waiting for a free network connection to continue the download.
>* `DownloadRunning`: The download is actively in progress.
>* `UpdateWaiting`: An update operation is underway.

The `contentParameters` field provides information such as:

* For `RoadMap` type:

  <!-- -->

  * `Copyright`: Value containing the copyright information for the road map
  * `MapDataProvider`: Value containing the name of the map data provider
  * `ReleaseDate`: Value containing the release date for the road map
  * `DefaultName`: Value containing the name of the item

* For `ViewStyleHighRes` type:
  <!-- -->
  * `Background-Color`: Value containing the background color in decimal format. Can be used to check if a style is a dark-mode or light-mode recommended style.

* For `HumanVoice` type:
  <!-- -->
  * UnlLanguage and voice-related parameters

The image can be retrieved via the `imagePreview` property:

* Kotlin
* Java

```kotlin
// Kotlin
val item: ContentStoreItem = // ... obtain from store
if (item.isImagePreviewAvailable()) {
    val image = item.imagePreview
    // Use the image object
}

```

```java
// Java
ContentStoreItem item = // ... obtain from store
if (item.isImagePreviewAvailable()) {
    UnlImage image = item.getImagePreview();
    // Use the image object
}

```

> 🚨 **DANGER**
>
>Content store items of type `RoadMap` do not have an image preview. The `MapDetails.getCountryFlag()` method can be used to get the flag image associated with a country code instead.
>
> Use the `countryCodes` property to obtain the country codes associated with a content store item of type `RoadMap`.

#### Fields Containing Download and Update Information[​](#fields-containing-download-and-update-information "Direct link to Fields Containing Download and Update Information")

| Field Name           | Description                                                |
| -------------------- | ---------------------------------------------------------- |
| `clientVersion`      | The client version of the content.                         |
| `updateVersion`      | The update version if an update is available.              |
| `downloadProgress`   | The current download progress as a percentage.             |
| `updateItem`         | The corresponding update item if an update is in progress. |
| `isCompleted()`      | Checks if the item has been completely downloaded.         |
| `isUpdatable()`      | Checks if the item has a newer version available.          |
| `canDeleteContent()` | Checks if the content can be deleted.                      |

While the download is in progress, you can retrieve details about the downloaded content:

* **`isCompleted()`**: Returns `true` if the download is completed; otherwise, returns `false`.
* **`downloadProgress`**: Returns the download progress as an integer between 0 and 100.
* **`status`**: Returns the current status of the content store item.

#### Fields Relevant to `EContentType.RoadMap` Type Items[​](#fields-relevant-to-econtenttyperoadmap-type-items "Direct link to fields-relevant-to-econtenttyperoadmap-type-items")

| Field Name     | Description                              |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `chapterName`  | Some large countries are divided into multiple content store items. All items within the same country share the same chapter. |
| `countryCodes` | A list of country codes (ISO 3166-1 alpha-3) associated with the product.                                                     |
| `language`     | The full language details for the product.                                                                                    |

#### Fields Relevant to `EContentType.HumanVoice` Type Items[​](#fields-relevant-to-econtenttypehumanvoice-type-items "Direct link to fields-relevant-to-econtenttypehumanvoice-type-items")

| Field Name     | Description                              |
| -------------- | ------------------------------------------------------------------------- |
| `countryCodes` | A list of country codes (ISO 3166-1 alpha-3) associated with the product. |
| `language`     | The full language details for the product.                                |

## Download Content Store Item[​](#download-content-store-item "Direct link to Download Content Store Item")

For downloading a content store item, the `asyncDownload` method can be used:

* Kotlin
* Java

```kotlin
// Kotlin
val item: ContentStoreItem = // ... obtain from store
val errorCode = item.asyncDownload(
    onProgress = {
        Log.d("Download", "Progress: $it%")
    },
    onCompleted = { error,_ ->
        if (error == UnlError.NoError) {
            Log.d("Download", "Completed successfully")
        } else {
            Log.e("Download", "Error: ${UnlError.getMessage(error)}")
        }
    }
)

```

```java
// Java
ContentStoreItem item = // ... obtain from store
int errorCode = item.asyncDownload(
    (progress) -> {
        Log.d("Download", "Progress: " + progress + "%");
    },
    (error, hint) -> {
        if (error == UnlError.NoError) {
            Log.d("Download", "Completed successfully");
        } else {
            Log.e("Download", "Error: " + UnlError.getMessage(error));
        }
    }
);

```

The `onCompleted` callback is invoked at the end of the operation, returning the result of the download. If the item is successfully downloaded, the error code is `UnlError.NoError`. In case of an error, other values may be returned.

Additionally, users can provide an optional `onProgress` callback to receive real-time progress updates. This callback is triggered with the current download progress, represented as an integer between 0 and 100.

### Download on Charged Networks[​](#download-on-charged-networks "Direct link to Download on Charged Networks")

The SDK includes functionality to restrict downloads on networks with additional charges.

To enable downloads on such networks, use the `allowChargedNetworks` parameter in the `asyncDownload` method:

* Kotlin
* Java

```kotlin
// Kotlin
val errorCode = item.asyncDownload(
    allowChargedNetworks = true,  // Allow downloads on charged networks
    onCompleted = { error,_ ->
        // Handle completion
    }
)

```

```java
// Java
int errorCode = item.asyncDownload(
    true,  // Allow downloads on charged networks
    (error, hint) -> {
        // Handle completion
    }
);

```

If a download operation is requested while the user is on a charged network and `allowChargedNetworks` is false, the download will be automatically scheduled and will proceed once the user switches to a non-charged network. In this case, the `status` field of the corresponding `ContentStoreItem` object will be set to `EContentStoreItemStatus.DownloadWaitingFreeNetwork`.

### Pause Download[​](#pause-download "Direct link to Pause Download")

The download can be paused using the `pauseDownload()` method. To resume the download, call `asyncDownload()` again.

* Kotlin
* Java

```kotlin
// Kotlin
val item: ContentStoreItem = // ... obtain from store
val errorCode = item.pauseDownload()
if (errorCode == UnlError.NoError) {
    Log.d("Download", "Download paused")
}

```

```java
// Java
ContentStoreItem item = // ... obtain from store
int errorCode = item.pauseDownload();
if (errorCode == UnlError.NoError) {
    Log.d("Download", "Download paused");
}

```

> 🚨 **DANGER**
>
> No further download operations should be performed on the `ContentStoreItem` object until the pause operation has completed.

### Delete Downloaded Content[​](#delete-downloaded-content "Direct link to Delete Downloaded Content")

Downloaded content can be removed from local storage by calling the `deleteContent()` method on the corresponding `ContentStoreItem` object:

* Kotlin
* Java

```kotlin
// Kotlin
val item: ContentStoreItem = // ... obtain from store
if (item.canDeleteContent()) {
    val errorCode = item.deleteContent()
    if (errorCode == UnlError.NoError) {
        Log.d("ContentStore", "Item ${item.name} deleted successfully")
    }
} else {
    Log.w("ContentStore", "Item cannot be deleted")
}

```

```java
// Java
ContentStoreItem item = // ... obtain from store
if (item.canDeleteContent()) {
    int errorCode = item.deleteContent();
    if (errorCode == UnlError.NoError) {
        Log.d("ContentStore", "Item " + item.getName() + " deleted successfully");
    }
} else {
    Log.w("ContentStore", "Item cannot be deleted");
}

```

> 🚨 **DANGER**
>
> Do not confuse the functionality provided by the `ContentStore` / `ContentStoreItem` classes with that of the `UnlMapDownloaderService` class.
>
>* The `ContentStore` API is designed for **downloading full offline content**, including data required for features such as free-text search, routing, and turn-by-turn navigation.
>* In contrast, the `UnlMapDownloaderService` is intended for caching map tiles mainly for visual display purposes. Tiles downloaded via `UnlMapDownloaderService` **do not support** most search operations, routing or navigation while offline.
>
> See the [download individual map tiles documentation](/04-Maps/03-Adjust%20the%20Map%20View.md#download-individual-map-tiles) for more details about the `UnlMapDownloaderService`.
>
> Also, do not confuse the `UnlLandmarkStore` class with the `ContentStore` class. The `UnlLandmarkStore` is a collection used for managing landmark data, while the `ContentStore` is a service used for managing offline map content.

### Delete Downloaded Content[​](#delete-downloaded-content-1 "Direct link to Delete Downloaded Content")

Downloaded content can be removed from local storage by calling the `deleteContent` method on the corresponding `ContentStoreItem` object, after checking if the item can be removed:

```dart
if (contentStoreItem.canDeleteContent){
    final error = contentStoreItem.deleteContent();
    showSnackbar("Item ${contentStoreItem.name} deletion resulted with code $error");
} else {
    showSnackbar("Item cannot be deleted");
}

```

> 🚨 **DANGER**
>
> Do not confuse the functionality provided by the `ContentStore` / `ContentStoreItem` classes with that of the `UnlMapDownloaderService` class.
>
>* The `ContentStore` API is designed for **downloading full offline content**, including data required for features such as free-text search, routing, and turn-by-turn navigation.
>* In contrast, the `UnlMapDownloaderService` is intended for caching map tiles mainly for visual display purposes. Tiles downloaded via `UnlMapDownloaderService` **do not support** most search operations, routing or navigation while offline.
>
> See the [download individual map tiles documentation](../04-Maps/03-Adjust%20the%20Map%20View.md#download-individual-map-tiles) for more details about the `UnlMapDownloaderService`.

Also, do not confuse the `UnlLandmarkStore` class with the `ContentStore` class. The `UnlLandmarkStore` is a collection used for managing landmark data, while the `ContentStore` is a service used for managing offline map content.

## Downloading overlays[​](#downloading-overlays "Direct link to Downloading overlays")

Overlays can be downloaded for specific regions to enable offline functionality. To do this, you must first download a map region, after which the overlays become available for download within those offline areas. The offline data grabber automatically downloads overlay data when enabled.

* Kotlin
* Java

```kotlin
// Kotlin
val overlayService = OverlayService()
val overlayId = ECommonOverlayId.Safety.value // Example overlay UID (e.g., speed cameras)

if (!overlayService.isOverlayOfflineDataGrabberSupported(overlayId)) {
    Log.w("OverlayService", "Overlay offline data grabber not supported for this overlay")
    return
}

// Enable the offline data grabber
val errorCode = overlayService.enableOverlayOfflineDataGrabber(overlayId)
if (errorCode == UnlError.NoError) {
    Log.d("OverlayService", "Offline data grabber enabled successfully")
} else {
    Log.e("OverlayService", "Failed to enable offline data grabber: ${UnlError.getMessage(errorCode)}")
}

// Disable the grabber when it's no longer needed
// val disableErrorCode = overlayService.disableOverlayOfflineDataGrabber(overlayId)

```

```java
// Java
OverlayService overlayService = new OverlayService();
int overlayId = ECommonOverlayId.Safety.getValue(); // Example overlay UID (e.g., speed cameras)

if (!overlayService.isOverlayOfflineDataGrabberSupported(overlayId)) {
    Log.w("OverlayService", "Overlay offline data grabber not supported for this overlay");
    return;
}

// Enable the offline data grabber
int errorCode = overlayService.enableOverlayOfflineDataGrabber(overlayId);
if (errorCode == UnlError.NoError) {
    Log.d("OverlayService", "Offline data grabber enabled successfully");
} else {
    Log.e("OverlayService", "Failed to enable offline data grabber: " + UnlError.getMessage(errorCode));
}

// Disable the grabber when it's no longer needed
// int disableErrorCode = overlayService.disableOverlayOfflineDataGrabber(overlayId);

```

> 🚨 **DANGER**
>
> Not all overlays support offline data grabbing. Use the `isOverlayOfflineDataGrabberSupported` method to check if a specific overlay supports this feature.

After enabling the offline data grabber, the overlay data will be automatically downloaded when a map region is downloaded or updated. The overlay items will then be available in offline mode within the downloaded map regions. You can verify if the overlay data has been successfully downloaded if overlay items are visible inside the downloaded map region in offline mode.

![Offline Speed Camera Overlay](../assets/images/example_android_offline_overlay_visible-f2c08dd016b614575567e240e483285d.png "Offline speed camera overlay item visible")

Offline speed camera overlay item visibl

> 💡 **TIP**
>
>Ensure to enable the offline data grabber using `enableOverlayOfflineDataGrabber` and verify the returned error code is `UnlError.NoError`. If the overlay UID is unsupported, the method will return an error code other than `UnlError.NoError`.

> 📝 **INFO**
>
> Not all overlay types support offline functionality (e.g., Alerts or Public Transit Stops). For instance, public transport stops will still require an internet connection to display the relevant data, thus will be rendered as landmarks instead of overlay items in offline mode.

You can check if the overlay data grabber has been enabled for a specific overlay using the `isOverlayOfflineDataGrabberEnabled` method. Keeping the grabber enabled will make the grabber automatically start downloading overlay data when a new map region is downloaded or updated. This enables the user to have the latest offline overlay data available.

## Download or update content in background[​](#download-or-update-content-in-background "Direct link to Download or update content in background")

To enable content updates and downloads while the app runs in the background, you must configure your app to support foreground services. This ensures that the Android system does not terminate the download process when the app is backgrounded.

For detailed guidance on implementing foreground services for background operations, please refer to the [Background Location](/05-Positioning%20&%20Sensors/08-Background%20Location.md) guide.
