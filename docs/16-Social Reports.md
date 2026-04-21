# Social reports

Social reports are user-generated alerts about real-time driving conditions or incidents on the road. These reports can include various types of information, such as accidents, police presence, road construction and more.

Users can create new reports, with the possibility to provide information such as category, name, image and other parameters. They can provide feedback, enabling voting on the accuracy of incidents and commenting on reported events. They can confirm or deny the validity of reports, delete their own reports, and contribute additional comments for further context. These interactions help enhance the accuracy and reliability of the information shared within the community.

Social reports are visible by all users with the social overlay enabled, given a compatible map style.

## Report categories[​](#report-categories "Direct link to Report categories")

The following categories and subcategories are provided in the form of a hierarchical structure, based on the `OverlayCategory` class:

```text
┌ Police Car (id 256)
│   - My Side (id 264)
│   - Opposite Side (id 272)
│   - Both Sides (280)
└■
┌ Fixed Camera (id 512)
│   - My Side (id 520)
│   - Opposite Side (id 528)
│   - Both Sides (536)
└■
┌ Traffic (id 768)
│   - Moderate (id 776)
│   - Heavy (id 784)
│   - Standstill (792)
└■
┌ Crash (id 1024)
│   - My Side (id 1032)
│   - Opposite Side (id 1040)
└■
┌ Crash (id 1024)
│   - My Side (id 1032)
│   - Opposite Side (id 1040)
└■
┌ Road Hazard (id 1280)
│   - Pothole (id 1288)
│   - Constructions (id 1296)
│   - Animals (id 1312)
│   - Object on Road (id 1328)
│   - Vehicle Stopped on Road (id 1344)
└■
┌ Weather Hazard (id 1536)
│   - Fog (id 1544)
│   - Ice on Road (id 1552)
│   - Flood (id 1560)
│   - Hail (id 1568)
└■
┌ Road Closure (id 3072)
│   - My Side (id 3080)
│   - Opposite Side (id 3088)
│   - Both Sides (id 3096)
└■

```

The main categories and subcategories can be retrieved via the following snippet:

* Kotlin
* Java

```kotlin
// Kotlin
val overlayInfo = SocialOverlay.reportsOverlayInfo
val categories = overlayInfo?.categories

categories?.forEach { category ->
    Log.d("SocialReports", "Category name: ${category.name}")
    Log.d("SocialReports", "Category id: ${category.uid}")

    category.subcategories?.forEach { subCategory ->
        Log.d("SocialReports", "Subcategory name: ${subCategory.name}")
        Log.d("SocialReports", "Subcategory id: ${subCategory.uid}")
    }
}

```

```java
// Java
OverlayInfo overlayInfo = SocialOverlay.getReportsOverlayInfo();
List<OverlayCategory> categories = overlayInfo != null ? overlayInfo.getCategories() : null;

if (categories != null) {
    for (OverlayCategory category : categories) {
        Log.d("SocialReports", "Category name: " + category.getName());
        Log.d("SocialReports", "Category id: " + category.getUid());

        List<OverlayCategory> subcategories = category.getSubcategories();
        if (subcategories != null) {
            for (OverlayCategory subCategory : subcategories) {
                Log.d("SocialReports", "Subcategory name: " + subCategory.getName());
                Log.d("SocialReports", "Subcategory id: " + subCategory.getUid());
            }
        }
    }
}

```

More details about the `OverlayCategory` class structure can be found in the [Overlay documentation](/03-Core/06-Overlays.md).

## Uploading a Social Report[​](#uploading-a-social-report "Direct link to Uploading a Social Report")

Before uploading a social report, it must first be prepared. The `SocialOverlay` class provides methods like `prepareReporting` and `prepareReportingWithCoordinates` to handle the report preparation phase.

The `prepareReporting` method takes a category ID and uses the current user's location or a specified data source, while `prepareReportingWithCoordinates` accepts both a category ID and a `UnlCoordinates` entity, enabling reporting from a different location. Those methods return an integer, called `prepareId` which is later passed to `report` method, in order to upload a social overlay item.

The following code snippet performs prepare and report of a social overlay item:

* Kotlin
* Java

```kotlin
// Kotlin
// Get the reporting id (uses current position)
val prepareIdOrError = SocialOverlay.prepareReporting()

if (prepareIdOrError <= 0) {
    // Handle error
    Log.e("SocialReports", "Prepare error: ${UnlError.getMessage(prepareIdOrError)}")
    return
}

// Get the subcategory id
val overlayInfo = SocialOverlay.reportsOverlayInfo ?: return
val countryISOCode = MapDetails().isoCodeForCurrentPosition ?: return
val categories = overlayInfo.getCategories(countryISOCode) ?: return
val mainCategory = categories.first()
val subcategories = mainCategory.subcategories ?: return
val subCategory = subcategories.first()

// Create progress listener
val progressListener = UnlProgressListener.create(
    onCompleted = { errorCode, hint ->
        Log.d("SocialReports", "Report result error: ${UnlError.getMessage(errorCode)}")
    }
)

// Report
val error = SocialOverlay.report(
    prepareId = prepareIdOrError,
    categoryId = subCategory.uid,
    listener = progressListener
)

```

```java
// Java
// Get the reporting id (uses current position)
int prepareIdOrError = SocialOverlay.prepareReporting();

if (prepareIdOrError <= 0) {
    // Handle error
    Log.e("SocialReports", "Prepare error: " + UnlError.getMessage(prepareIdOrError));
    return;
}

// Get the subcategory id
OverlayInfo overlayInfo = SocialOverlay.getReportsOverlayInfo();
if (overlayInfo == null) return;
String countryISOCode = new MapDetails().getIsoCodeForCurrentPosition();
if (countryISOCode == null) return;
List<OverlayCategory> categories = overlayInfo.getCategories(countryISOCode);
if (categories == null) return;
OverlayCategory mainCategory = categories.get(0);
List<OverlayCategory> subcategories = mainCategory.getSubcategories();
if (subcategories == null) return;
OverlayCategory subCategory = subcategories.get(0);

// Create progress listener
UnlProgressListener progressListener = UnlProgressListener.create(
    (errorCode, hint) -> {
        Log.d("SocialReports", "Report result error: " + UnlError.getMessage(errorCode));
    }
);

// Report
int error = SocialOverlay.report(
    prepareIdOrError,
    subCategory.getUid(),
    progressListener
);

```

If you want to use a location from a specific data source, you can pass the data source to the `prepareReporting` method, as shown below:

* Kotlin
* Java

```kotlin
// Kotlin
// Assuming 'ds' is an instance of a valid DataSource
val prepareIdOrError = SocialOverlay.prepareReporting(dataSource = ds)

```

```java
// Java
// Assuming 'ds' is an instance of a valid DataSource
int prepareIdOrError = SocialOverlay.prepareReporting(ds);

```

> 🚨 **DANGER**
>
> `prepareReporting` must be called with a `DataSource` whose position data is classified as **high accuracy** by the map-matching system (the only exception is the `Weather Hazard` category, `categId = 1536`, which does not require accurate positioning). If a high-accuracy data source is not provided, the method will return `UnlError.notFound` and the report cannot be created.

The report is displayed for a limited duration before being automatically removed.

The report result will be provided via the `onCompleted` callback, with the following `UnlError` values:

* `InvalidInput` if the category id is invalid/ the parameters are ill formatted or if the snapshot is an invalid image.
* `Suspended` if the rate limit for the user is exceeded.
* `Expired` if the prepared report is too old.
* `NotFound` if no accurate data source is detected.
* `NoError` if the operation has succeeded

The method returns an error code which can be checked with `UnlError.isError()`. If the operation could not be started, the method returns an error code.

> 🚨 **DANGER**
>
> Most report categories require the use of the `prepareReporting` method, ensuring higher report accuracy by confirming the user's proximity to the reported location. See the [Get started with Positioning](../05-Positioning%20&%20Sensors/03-Get%20Started%20with%20Positioning.md) guide for more information about configuring the data source.
>
> The `prepareReportingWithCoordinates` method works only for `Weather Hazard` categories and subcategories contained within.

> 🚨 **DANGER**
>
> While reporting events, the `prepareReporting` method needs to be in preparing mode (categoryId=0) rather than dry run mode (categoryId != 0).

> 💡 **TIP**
>
> The `report` function accepts the following optional parameters:
>
>* `snapshot`: Used to provide an image for the report. This should be an `UnlImage` object.
>* `description`: A string description for the report.
>* `params`: A `ParameterList` configuration object that allows further customization of the report details.
>
> These parameters are optional and can be omitted if not needed.

## Updating a Social Report[​](#updating-a-social-report "Direct link to Updating a Social Report")

In order to update an existing report's parameters the `SocialOverlay.updateReport(item, params, listener)` method can be used as shown below:

* Kotlin
* Java

```kotlin
// Kotlin
val overlays = mapView.cursorSelectionOverlayItems
val overlayItem = overlays?.firstOrNull() ?: return

val params = overlayItem.getPreviewData() ?: return
// Find and update specific parameters as needed
val locationParam = params.find { it.key == "location_address" }
locationParam?.run {
    val updatedParam = Parameter(key!!, "New address",name )
    assign(updatedParam)
}

val progressListener = UnlProgressListener.create(
    onCompleted = { errorCode, hint ->
        Log.d("SocialReports", "Update result error: ${UnlError.getMessage(errorCode)}")
    }
)

val error = SocialOverlay.updateReport(
    item = overlayItem,
    params = params,
    listener = progressListener
)

```

```java
// Java
List<OverlayItem> overlays = mapView.getCursorSelectionOverlayItems();
if (overlays == null || overlays.isEmpty()) return;
OverlayItem overlayItem = overlays.get(0);

ParameterList params = overlayItem.getPreviewData();
if (params == null) return;
// Find and update specific parameters as needed
Parameter locationParam = null;
for (Parameter p : params) {
    if ("location_address".equals(p.getKey())) {
        locationParam = p;
        break;
    }
}
if (locationParam != null) {
    Parameter updatedParam = new Parameter(locationParam.getKey(), "New address", locationParam.getName());
    locationParam.assign(updatedParam);
}

UnlProgressListener progressListener = UnlProgressListener.create(
    (errorCode, hint) -> {
        Log.d("SocialReports", "Update result error: " + UnlError.getMessage(errorCode));
    }
);

int error = SocialOverlay.updateReport(
    overlayItem,
    params,
    progressListener
);

```

The structure of the `ParameterList` object passed to the `updateReport` method should follow the structure returned by the `OverlayItem`'s `getPreviewData()`. The keys of the fields accepted can be found inside predefined overlay parameter constants.

The `updateReport` method might provide the following `UnlError` values via the `onCompleted` callback:

* `InvalidInput` if the `ParameterList`'s structure is incorrect.
* `NoError` if the operation has been successfully completed.

> 💡 **TIP**
>
> A user can obtain a report `OverlayItem` through the following methods:
>
>* **Map Selection**: By selecting an item directly from the map using the `cursorSelectionOverlayItems()` method provided by the `UnlMapView`.
>* **Search**: By performing a search that includes preferences configured to return overlay items.
>* **Proximity Alerts**: Via the `UnlAlarmListener`, when approaching a report that triggers an alert.

## Deleting a Social Report[​](#deleting-a-social-report "Direct link to Deleting a Social Report")

This is accomplished through `SocialOverlay.deleteReport(overlayItem, listener)`, with the restriction that only the original creator of the report has the authority to delete it.

The `deleteReport` method might provide the following `UnlError` values on the `onCompleted` method:

* `InvalidInput` if the item is not a social report overlay item or not the result of an alarm notification.
* `AccessDenied` if the user does not have the required rights.
* `NoError` if the operation was successfully completed.

## Interact with a Social Report[​](#interact-with-a-social-report "Direct link to Interact with a Social Report")

### Provide positive feedback[​](#provide-positive-feedback "Direct link to Provide positive feedback")

Users can provide positive feedback for a reported event using `SocialOverlay.confirmReport(overlayItem, listener)`, which increases the value of the score key within the `OverlayItem` preview data.

### Provide negative feedback[​](#provide-negative-feedback "Direct link to Provide negative feedback")

If a report is found to be inaccurate, it can be denied by other users using `SocialOverlay.denyReport(overlayItem, listener)`, which accepts an `OverlayItem` object as its parameter.

If a report gets many downvotes it will be removed.

Both `confirmReport` and `denyReport` will provide the following `UnlError` values using the `onCompleted` callback:

* `InvalidInput` if the item is not a social report overlay item or not the result of an alarm notification.
* `AccessDenied` if the user already voted.
* `NoError` if the operation was successfully completed.

### Add comment[​](#add-comment "Direct link to Add comment")

Additionally, users can contribute comments to a reported event by calling `SocialOverlay.addComment`, as shown below:

* Kotlin
* Java

```kotlin
// Kotlin
val progressListener = UnlProgressListener.create(
    onCompleted = { errorCode, hint ->
        Log.d("SocialReports", "Add comment result error: ${UnlError.getMessage(errorCode)}")
    }
)

val error = SocialOverlay.addComment(
    item = overlayItem,
    comment = "This is a comment",
    listener = progressListener
)

```

```java
// Java
UnlProgressListener progressListener = UnlProgressListener.create(
    (errorCode, hint) -> {
        Log.d("SocialReports", "Add comment result error: " + UnlError.getMessage(errorCode));
    }
);

int error = SocialOverlay.addComment(
    overlayItem,
    "This is a comment",
    progressListener
);

```

Added comments can be viewed within the `OverlayItem`'s preview data

The `addComment` method will provide the following `UnlError` values on the `onCompleted` callback:

* `InvalidInput` if the item is not a social report overlay item or not the result of an alarm notification.
* `ConnectionRequired` if no internet connection is available.
* `Busy` if another comment operation is in progress.
* `NoError` if the comment was added.

## Get updates about a report[​](#get-updates-about-a-report "Direct link to Get updates about a report")

A user may be interested in tracking changes to a report - whether they made changes or not. For this purpose, the `SocialOverlayItemListener` class can be used.

To create and register a listener for a specific `OverlayItem` report, refer to the following snippet:

* Kotlin
* Java

```kotlin
// Kotlin
val listener = SocialOverlayItemListener.create(
    onOverlayItemUpdated = { report ->
        Log.d("SocialReports", "The report has been updated")
    }
)

SocialOverlay.registerOverlayItemListener(overlayItem , listener)

```

```java
// Java
SocialOverlayItemListener listener = SocialOverlayItemListener.create(
    report -> {
        Log.d("SocialReports", "The report has been updated");
    }
);

SocialOverlay.registerOverlayItemListener(overlayItem, listener);

```

The `registerOverlayItemListener` method does not return an error code. The registration happens directly without validation errors.

In order to unregister the listener:

* Kotlin
* Java

```kotlin
// Kotlin
SocialOverlay.unregisterOverlayItemListener(overlayItem, listener)

```

```java
// Java
SocialOverlay.unregisterOverlayItemListener(overlayItem, listener);

```

The `unregisterOverlayItemListener` method does not return an error code. The unregistration happens directly.
