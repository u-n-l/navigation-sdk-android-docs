# Landmarks

A **landmark** is a predefined, permanent location that holds detailed information such as its name, address, description, geographic area, categories (e.g., Gas Station, Shopping), entrance locations, contact details, and sometimes associated multimedia (e.g., icons or images). It represents significant, categorized locations with rich metadata, providing structured context about a place.

Landmarks are typically used in mapping and navigation applications to help users identify and interact with important locations. They can be points of interest (POIs) like restaurants, parks, museums, or any other notable places that users might want to find or navigate to.

## Landmark Structure[​](#landmark-structure "Direct link to Landmark Structure")

#### Geographic Details[​](#geographic-details "Direct link to Geographic Details")

A landmark's position is defined by its `coordinates`, which represent the centroid, and its `geographicArea`, representing the full boundary (e.g., circle, rectangle, or polygon). Since landmarks can correspond to buildings, roads, settlements, or regions, the geographic area can be complex. For specific bounding areas, the `getContourGeographicArea` method is used.

#### Descriptive Information[​](#descriptive-information "Direct link to Descriptive Information")

Landmarks include attributes like `name`, `description`, and `author`. The name adapts to the SDK's language settings, ensuring localization where applicable.

#### Metadata[​](#metadata "Direct link to Metadata")

Landmarks can belong to one or more `categories`, described by `UnlLandmarkCategory`. Additional details like `contactInfo` ( `UnlContactInfo` is a specialized class for storing common data like phone and email) and `extraInfo` (a structured hashmap) add flexibility for storing metadata.

Example of how to set metadata:

* Kotlin
* Java

```kotlin
// Kotlin
val landmark = UnlLandmark()
val contactInfo = UnlContactInfo()
contactInfo.addField(EUnlContactFieldType.Email, "your_address@yourdomain.com", "business email")
contactInfo.addField(EUnlContactFieldType.Phone, "+123456789", "business phone")
landmark.contactInfo = contactInfo
landmark.extraInfo = arrayListOf("Working hours: 10:00-18:00")

```

```java
// Java
UnlLandmark landmark = new UnlLandmark();
UnlContactInfo contactInfo = new UnlContactInfo();
contactInfo.addField(EUnlContactFieldType.Email, "your_address@yourdomain.com", "business email");
contactInfo.addField(EUnlContactFieldType.Phone, "+123456789", "business phone");
landmark.setContactInfo(contactInfo);
ArrayList<String> extraInfo = new ArrayList<>();
extraInfo.add("Working hours: 10:00-18:00");
landmark.setExtraInfo(extraInfo);

```

#### Media and Imagery[​](#media-and-imagery "Direct link to Media and Imagery")

Images associated with landmarks can be retrieved using getters like `image` (primary image) or `extraImage` (secondary images). Please note that these images might contain invalid data and it is the user's responsability to check the validity of the objects using the provided methods.

#### Advanced Metadata[​](#advanced-metadata "Direct link to Advanced Metadata")

Attributes such as `landmarkStoreId`, `landmarkStoreType` provide information about assigned landmark store, the landmark store type. The `timeStamp` records information about the time the landmark was inserted into a store.

#### Address[​](#address "Direct link to Address")

The `address` attribute connects landmarks to `UnlAddressInfo`, providing details about the physical address of the location.

#### Unique Identifier[​](#unique-identifier "Direct link to Unique Identifier")

The `id` ensures every landmark is uniquely identifiable. Its value is initially **-1** if it does not have an associated ID. You can't modify this value. The `id` is set when the landmark is added to a store.

## Instantiating Landmarks[​](#instantiating-landmarks "Direct link to Instantiating Landmarks")

Landmarks can be instantiated in multiple ways:

1. **Default Initialization**: `UnlLandmark()` creates a basic landmark object.
2. **Using Latitude & Longitude**: `UnlLandmark(name,latitude,longitude)` ties a landmark to geographic coordinates.
3. **With a UnlCoordinates Object**: `UnlLandmark(name,coordinates)` uses a predefined `UnlCoordinates` object.

> 🚨 **Danger**
>
> Creating a new landmark does not automatically make it visible on the map. Refer to the [Display landmarks](/04-Maps/05-Display%20Map%20Items/02-Display%20Landmarks.md) guide for detailed instructions on how to display a landmark.

## Copy Landmarks[​](#copy-landmarks "Direct link to Copy Landmarks")

Landmarks can be duplicated using the `assign` method, which creates a new landmark instance with identical properties.

* Kotlin
* Java

```kotlin
// Kotlin
val originalLandmark = UnlLandmark("Original", 40.7128, -74.0060)
val copiedLandmark = UnlLandmark().assign(originalLandmark)

```

```java
// Java
UnlLandmark originalLandmark = new UnlLandmark("Original", 40.7128, -74.0060);
UnlLandmark copiedLandmark = new UnlLandmark().assign(originalLandmark);

```

## Interaction with Landmarks[​](#interaction-with-landmarks "Direct link to Interaction with Landmarks")

### Selecting landmarks[​](#selecting-landmarks "Direct link to Selecting landmarks")

Landmarks are selectable by default, meaning user interactions, such as taps or clicks, can identify specific landmarks programmatically (e.g., through the function `cursorSelectionLandmarks()`). Refer to the [UnlLandmark selection guide](/04-Maps/04-Interact%20with%20the%20Map.md#landmark-selection) for more details.

### Highlighting landmarks[​](#highlighting-landmarks "Direct link to Highlighting landmarks")

A list of landmarks can be highlighted, enabling customization of their visual appearance. Highlighting also allows displaying a list of user created landmarks on the map. When displaying them, we can also provide an identifier for the highlight. It is possible to de-activate that highlight or to update it - in this case the old highlight is overridden. Refer to the [Highlight landmarks](/04-Maps/05-Display%20Map%20Items/02-Display%20Landmarks.md#highlight-landmarks) guide for more details.

### Searching landmarks[​](#searching-landmarks "Direct link to Searching landmarks")

Landmarks are searchable (both landmarks from map and user created landmarks). Search can be performed based on name, geographic location, proximity to a given route, address, and more. Options to filter the search based on landmark categories are available. Refer to the [Get started with Search](/06-Search/02-Get%20Started%20with%20Search.md) guide for more details.

### Calculating route with landmarks[​](#calculating-route-with-landmarks "Direct link to Calculating route with landmarks")

Landmarks are the sole entities used for route calculations. For detailed guidance, refer to the [Get started with Routing](/07-Routing/02-Get%20Started%20with%20Routing.md) guide.

### Get notifications when approaching landmarks[​](#get-notifications-when-approaching-landmarks "Direct link to Get notifications when approaching landmarks")

Alarms can be configured to notify users when approaching specific landmarks that have been custom-selected. See the [Landmarks and overlay alarms](/01-Overview/04-Todo.md) guide for more details about implementing this feature.

## Other usages[​](#other-usages "Direct link to Other usages")

* Most map POIs (such as settlements, roads, addresses, businesses, etc.) are landmarks.
* Search results return a list of landmarks.
* Intermediary points in a Route are landmarks.

## Landmark Categories[​](#landmark-categories "Direct link to Landmark Categories")

Landmarks are categorized based on their assigned categories. Each category is defined by a unique ID, an image (which can be used in various UI components created by the SDK user), and a name that is localized based on the language set for the SDK in the case of default categories. Additionally, a landmark may be associated with a parent landmark store if assigned to one.

> 📝 **Info**
>
> A single landmark can belong to multiple categories simultaneously.

### Predefined generic categories[​](#predefined-generic-categories "Direct link to Predefined generic categories")

The default landmark categories are presented below:

| **Category**                     | **Description**                                                               |
| -------------------------------- | ----------------------------------------------------------------------------- |
| **GasStation**                   | Locations where fuel is available for vehicles.                               |
| **Parking**                      | Designated areas for vehicle parking, including public and private lots.      |
| **FoodAndDrink**                 | Places offering food and beverages, such as restaurants, cafes, or bars.      |
| **Accommodation**                | Facilities providing lodging, including hotels, motels, and hostels.          |
| **MedicalServices**              | Healthcare facilities like hospitals, clinics, and pharmacies.                |
| **Shopping**                     | Retail stores, shopping malls, and markets for purchasing goods.              |
| **CarServices**                  | Auto repair shops, car washes, and other vehicle maintenance services.        |
| **PublicTransport**              | Locations associated with buses, trains, trams, and other public transit.     |
| **Wikipedia**                    | Points of interest with available Wikipedia information for added context.    |
| **Education**                    | Educational institutions such as schools, universities, and training centers. |
| **Entertainment**                | Places for leisure activities, such as cinemas, theaters, or amusement parks. |
| **PublicServices**               | Government or civic buildings like post offices and administrative offices.   |
| **GeographicalArea**             | Specific geographical zones or regions of interest.                           |
| **Business**                     | Office buildings, corporate headquarters, and other business establishments.  |
| **Sightseeing**                  | Tourist attractions, landmarks, and scenic points of interest.                |
| **ReligiousPlaces**              | Places of worship, such as churches, mosques, temples, or synagogues.         |
| **Roadside**                     | Features or amenities located along the side of roads, such as rest areas.    |
| **Sports**                       | Facilities for sports and fitness activities, like stadiums and gyms.         |
| **Uncategorized**                | Landmarks that do not fall into any specific category.                        |
| **Hydrants**                     | Locations of water hydrants, typically for firefighting purposes.             |
| **EmergencyServicesSupport**     | Facilities supporting emergency services, such as dispatch centers.           |
| **CivilEmergencyInfrastructure** | Infrastructure related to emergency preparedness, such as shelters.           |
| **ChargingStation**              | Stations for charging electric vehicles.                                      |
| **BicycleChargingStation**       | Locations where bicycles can be charged, typically for e-bikes.               |
| **BicycleParking**               | Designated parking areas for bicycles.                                        |

The id for each category can be found in the `EGenericCategoriesIDs` enum, each value having assigned a `value`. Use the `getCategory` method from the `UnlGenericCategories` class to get the `UnlLandmarkCategory` class associated with a `EGenericCategoriesIDs` value.

* Kotlin
* Java

```kotlin
// Kotlin
val landmarkCategory = UnlGenericCategories().getCategory(EGenericCategoriesIDs.FoodAndDrink.value)

```

```java
// Java
UnlLandmarkCategory landmarkCategory = new UnlGenericCategories().getCategory(EGenericCategoriesIDs.FoodAndDrink.getValue());

```

> ⚠️ **Warning**
>
> There are 2 functions in `UnlGenericCategories` class: `getCategory` and get `getGenericCategory`. The first is the one that is explained above, while the second is used to get the generic landmark category associated with a **POI category**.
>
> * Kotlin
> * Java
>
>```kotlin
>// Kotlin
>val storeService = UnlLandmarkStoreService()
>val store = storeService.getLandmarkStoreById(storeService.> mapPoisLandmarkStoreId)
>val mapPoiCategory = store?.categories?.find { it.name == "Coffee Shop" }
>mapPoiCategory?.let { category ->
>    val landmarkCategory = UnlGenericCategories().getGenericCategory(category.id)
>    Log.d("CATEGORY", " ${landmarkCategory?.name} ") // ->> Food and Drink
>}
>
>```

>```java
>// Java
>UnlLandmarkStoreService storeService = new UnlLandmarkStoreService();
>UnlLandmarkStore store = storeService.getLandmarkStoreById(storeService.>getMapPoisLandmarkStoreId());
>if (store != null && store.getCategories() != null) {
>    UnlLandmarkCategory mapPoiCategory = null;
>    for (UnlLandmarkCategory cat : store.getCategories()) {
>        if ("Coffee Shop".equals(cat.getName())) {
>            mapPoiCategory = cat;
>            break;
>        }
>    }
>    if (mapPoiCategory != null) {
>        UnlLandmarkCategory landmarkCategory = new UnlGenericCategories().>getGenericCategory(mapPoiCategory.getId());
>        if (landmarkCategory != null) {
>            Log.d("CATEGORY", " " + landmarkCategory.getName() + " "); // ->> >Food and Drink
>        }
>    }
>}
>
>```

In addition to the predefined categories, custom landmark categories can be created, offering flexibility to define tailored classifications for specific needs or applications.

### Tree structure[​](#tree-structure "Direct link to Tree structure")

Each **generic landmark category** can include multiple **poi subcategories**. The `UnlLandmarkCategory` is used both for generic categories and poi subcategories.

For example, the *Parking* generic category contains *Park and Ride*, *Parking Garage*, *Parking Lot*, *RV Park*, *Truck Parking*, *Truck Stop* and *Parking meter* poi subcategories.

To retrieve poi subcategories for a generic category, use the `getPoiCategories` method provided in the `UnlGenericCategories` class. To find the parent generic category of a given subcategory use the `getGenericCategory` method provided in the `UnlGenericCategories` class.

### Usage[​](#usage "Direct link to Usage")

* Can be used as a filter parameter within the various types of search.
* Landmark visibility on the map can be toggled based on the categories.
* Landmark organization within a store

## Landmark Stores[​](#landmark-stores "Direct link to Landmark Stores")

**Landmark stores** are the primary collections used for organizing landmarks across the SDK. Each store contains landmarks and landmark categories, and is identified by a unique name and id. Stores are persisted on the device as SQLite database files and remain accessible across sessions.

### Manage landmark stores[​](#manage-landmark-stores "Direct link to Manage landmark stores")

The operations related to UnlLandmarkStore management can be found within the `UnlLandmarkStoreService` class.

#### Create a new landmark store[​](#create-a-new-landmark-store "Direct link to Create a new landmark store")

A new landmark store can be created in the following way:

* Kotlin
* Java

```kotlin
// Kotlin
val (store , errorCode) = UnlLandmarkStoreService().createLandmarkStore("MyLandmarkStore") ?: Pair(null, UnlError.General)

```

```java
// Java
Pair<UnlLandmarkStore, Integer> result = new UnlLandmarkStoreService().createLandmarkStore("MyLandmarkStore");
UnlLandmarkStore store = result != null ? result.first : null;
int errorCode = result != null ? result.second : UnlError.General;

```

The `createLandmarkStore` will create a new landmark store with the given name if it does not already exist or will return the existing landmark store if an instance with the given name already exists. The method also provides optional parameters to set the zoom level at which the landmarks contained within are visible on the map and a custom file path for storing the landmark on the file system can also be provided. The method returns a pair consisting of the created or existing landmark store and an error code indicating the success or failure of the operation.

> 🚨 **Danger**
>
> Since the landmark store is persistent, the `createLandmarkStore` method may return a `UnlLandmarkStore` instance that already contains landmarks and categories from previous sessions, if a store with the same name was created before.

#### Get landmark store by id[​](#get-landmark-store-by-id "Direct link to Get landmark store by id")

A landmark store can be obtained by its id:

* Kotlin
* Java

```kotlin
// Kotlin
UnlLandmarkStoreService().getLandmarkStoreById(1234)

```

```java
// Java
new UnlLandmarkStoreService().getLandmarkStoreById(1234);

```

The `getLandmarkStoreById` method retrieves a valid `UnlLandmarkStore` object when provided with a valid ID. If the ID does not correspond to any `UnlLandmarkStore`, the method returns null.

#### Get landmark store by name[​](#get-landmark-store-by-name "Direct link to Get landmark store by name")

A landmark store can be obtained by its name:

* Kotlin
* Java

```kotlin
// Kotlin
UnlLandmarkStoreService().getLandmarkStoreByName("MyLandmarkStore")

```

```java
// Java
new UnlLandmarkStoreService().getLandmarkStoreByName("MyLandmarkStore");

```

The `getLandmarkStoreByName` method returns a valid `UnlLandmarkStore` object if a landmark store exists with the given name and null if the given name does not correspond to any `UnlLandmarkStore`.

#### Get all landmark stores[​](#get-all-landmark-stores "Direct link to Get all landmark stores")

The list of landmark stores can be retrieved via the `landmarkStores` getter:

* Kotlin
* Java

```kotlin
// Kotlin
val landmarkStores = UnlLandmarkStoreService().landmarkStores

```

```java
// Java
List<UnlLandmarkStore> landmarkStores = new UnlLandmarkStoreService().getLandmarkStores();

```

> 📝 **Info**
>
> The `landmarkStores` getter returns both user created landmark stores and predefined stores in the UNL Navigation SDK for Android.

#### Remove landmark stores[​](#remove-landmark-stores "Direct link to Remove landmark stores")

A landmark store can be removed in the following way:

* Kotlin
* Java

```kotlin
// Kotlin
val storeId = landmarkStore.id
landmarkStore.release()
UnlLandmarkStoreService().removeLandmarkStore(storeId)

```

```java
// Java
int storeId = landmarkStore.getId();
landmarkStore.release();
new UnlLandmarkStoreService().removeLandmarkStore(storeId);

```

This will also remove the store from the persistent storage.

> 🚨 **Danger**
>
> Disposing the `UnlLandmarkStore` object is mandatory before calling the `removeLandmarkStore` method. If the store is not disposed then it **will not be removed**. Any operation called on a disposed `UnlLandmarkStore` instance will result in an exception being thrown. Therefore, it is crucial to obtain the landmark store ID before it is disposed.
>
> The `removeLandmarkStore` method will fail if the store is in use (for example, if it is displayed on the map or monitored within the `UnlAlarmService`). In this case, the `ApiErrorService.apiError` will be set to `UnlError.inUse`.

#### Retrieving the landmark store type[​](#retrieving-the-landmark-store-type "Direct link to Retrieving the landmark store type")

The type of a `UnlLandmarkStore` can be returned by calling `UnlLandmarkStoreService.getLandmarkStoreType` with the store ID.

* Kotlin
* Java

```kotlin
// Kotlin
val storeType = UnlLandmarkStoreService().getLandmarkStoreType(landmarkStore.id)

```

```java
// Java
int storeType = new UnlLandmarkStoreService().getLandmarkStoreType(landmarkStore.getId());

```

#### Predefined landmark stores[​](#predefined-landmark-stores "Direct link to Predefined landmark stores")

The Navigation SDK for Android includes several predefined landmark stores, and their IDs can be retrieved as follows:

* Kotlin
* Java

```kotlin
// Kotlin
val landmarkStoreService = UnlLandmarkStoreService()
val mapPoisLandmarkStoreId = landmarkStoreService.mapPoisLandmarkStoreId
val mapAddressLandmarkStoreId = landmarkStoreService.mapAddressLandmarkStoreId
val mapCitiesLandmarkStoreId = landmarkStoreService.mapCitiesLandmarkStoreId

```

```java
// Java
UnlLandmarkStoreService landmarkStoreService = new UnlLandmarkStoreService();
int mapPoisLandmarkStoreId = landmarkStoreService.getMapPoisLandmarkStoreId();
int mapAddressLandmarkStoreId = landmarkStoreService.getMapAddressLandmarkStoreId();
int mapCitiesLandmarkStoreId = landmarkStoreService.getMapCitiesLandmarkStoreId();

```

These values can be useful when determining whether a landmark originated from the default map elements. If its associated `landmarkStoreId` has one of these 3 values, it means it comes from the map rather than from other custom data.

> 🚨 **Danger**
>
> These three landmark stores are not intended to be modified. They are only used for functionalities like:
>
> * Filter the categories of landmarks displayed on the map.
> * Check whether a landmark comes from the map.
> * When searching, to filter the significant landmarks.
> * When using alarms, to filter the significant landmarks.

#### Import landmark stores[​](#import-landmark-stores "Direct link to Import landmark stores")

You can import a landmark store using data from a file or a raw data buffer.

The `importLandmarks` and `importLandmarksBuffer` methods import data into an existing `UnlLandmarkStore`. Before calling these methods, you must first create or retrieve the target UnlLandmarkStore. The import can be performed from a supported file format (e.g., KML, GeoJSON) or directly from a raw data buffer. You can assign a category and a custom image to the imported landmarks. A UnlProgressListener is returned to monitor the import progress, and an optional callback can be used to handle completion and error states.

* Kotlin
* Java

```kotlin
// Kotlin
landmarkStore.importLandmarks(
    "Your/path/file",
    ELandmarkFileFormat.Kml,
    UnlImage.produceWithPath("img/path/file"),
    UnlProgressListener.create(onCompleted = { err, msg ->
        if (UnlError.isError(err)) {
            //handle error
        } else {
            //...
        }
    })
)

```

```java
// Java
landmarkStore.importLandmarks(
    "Your/path/file",
    ELandmarkFileFormat.Kml,
    UnlImage.produceWithPath("img/path/file"),
    UnlProgressListener.create((err, msg) -> {
        if (UnlError.isError(err)) {
            //handle error
        } else {
            //...
        }
    })
);

```

**Parameters:**

* `filePath`: The file path of the landmark file to import.
* `format`: The file format (e.g., KML, GeoJSON), see `LandmarkFileFormat`.
* `image`: An image to associate with the landmarks.
* `onComplete`: A callback invoked once the import finishes, providing a `UnlError`.

#### Import from data buffer[​](#import-from-data-buffer "Direct link to Import from data buffer")

Landmark stores can also be imported from a raw data buffer:

* Kotlin
* Java

```kotlin
// Kotlin
landmarkStore.importLandmarksBuffer(
    dataBuffer,
    ELandmarkFileFormat.Kml,
    UnlImage.produceWithPath("img/path/file"),
    UnlProgressListener.create(onCompleted = { err, msg ->
        if (UnlError.isError(err)) {
            //handle error
        } else {
            //...
        }
    })
)

```

```java
// Java
landmarkStore.importLandmarksBuffer(
    dataBuffer,
    ELandmarkFileFormat.Kml,
    UnlImage.produceWithPath("img/path/file"),
    UnlProgressListener.create((err, msg) -> {
        if (UnlError.isError(err)) {
            //handle error
        } else {
            //...
        }
    })
);

```

**Parameters:**

* `buffer`: The binary data representing the landmark file content.
* `format`: The file format (e.g., KML, GeoJSON), see `LandmarkFileFormat`.
* `image`: A map image to associate with the landmarks.
* `onComplete`: A callback triggered upon completion with a `UnlError`.

> 💡 **Tip**
>
> Use this method when you receive the data as a binary.

### Browse landmark stores[​](#browse-landmark-stores "Direct link to Browse landmark stores")

The `LandmarkBrowseSession` class provides an efficient way to browse and search within a `UnlLandmarkStore` that contains a large number of landmarks.

To create a `LandmarkBrowseSession`, use the `createLandmarkBrowseSession` method available in the `UnlLandmarkStore` class. This method takes a `LandmarkBrowseSessionSettings` object as an argument, which defines the sorting and filtering criteria for the session.

* Kotlin
* Java

```kotlin
// Kotlin
val browsingSession = landmarkStore.createLandmarkBrowseSession(
    LandmarkBrowseSessionSettings().apply {
        //configure settings
    }
)

```

```java
// Java
LandmarkBrowseSessionSettings settings = new LandmarkBrowseSessionSettings();
//configure settings
LandmarkBrowseSession browsingSession = landmarkStore.createLandmarkBrowseSession(settings);

```

> 🚨 **Danger**
>
> Only the landmark contained within the store before the session creation are available in the browse session.

The available sorting and filter settings are:

| Field Name         | Type            | Default Value                          | Description                                                                                                                                                     |
| ------------------ | --------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `descendingOrder`  | `Boolean`       | `false`                                | Specifies whether the sorting of landmarks should be in descending order. By default, sorting is ascending.                                                     |
| `orderBy`          | `LandmarkOrder` | `LandmarkOrder.name`                   | Specifies the criteria used for sorting landmarks. By default, landmarks are sorted by name. Other options may include sorting by distance, and insertion date. |
| `nameFilter`       | `String`        | empty string                           | A filter applied to landmark names. Only landmarks that match this name substring will be included.                                                             |
| `categoryIdFilter` | `Integer`       | `UnlLandmarkStore.invalidLandmarkCategId` | Used to filter landmarks by category ID. The default value is considered invalid, meaning all categories are matched.                                           |
| `coordinates`      | `UnlCoordinates`   | an invalid instance                    | Specifies a point of reference used when ordering landmarks by distance. Only relevant when `orderBy == LandmarkOrder.distance`.                                |

The operations available within the `LandmarkBrowseSesssion` are:

| Member                                        | Type                                     | Description                                                                                           |
| --------------------------------------------- | ---------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `id`                                          | `Integer` (getter)                       | Retrieves the unique ID of this session.                                                              |
| `landmarkStoreId`                             | `Integer` (getter)                       | Returns the ID of the associated `UnlLandmarkStore`.                                                     |
| `landmarksCount`                              | `Integer` (getter)                       | Gets the total number of landmarks in this session.                                                   |
| `getLandmarks(start : Integer, end: Integer)` | `UnlLandmarkList?`                          | Retrieves landmarks between indices `[start, end)`. Used for pagination or slicing the landmark list. |
| `getLandmarkPos(landmarkId : Integer)`        | `Integer`                                | Returns the 0-based index of a landmark by its ID, or a not-found code.                               |
| `settings`                                    | `LandmarkBrowseSessionSettings` (getter) | Gets the current session settings. Modifying this object does not affect the session.                 |

### Available operations[​](#available-operations "Direct link to Available operations")

The `UnlLandmarkStore` instance provides the following operations for managing landmarks and categories:

| Operation                                | Description                                                                                                                                                                                                                                                                                                               |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `addCategory(category:UnlLandmarkCategory)` | Adds a new category to the store. The category must have a name. After addition, the category belongs to this store.                                                                                                                                                                                                      |
| `addLandmark`                            | Adds a **copy of the landmark** to a specified category in the store. Updates category info if the landmark already exists. Can specify a category. Defaults to uncategorized if no category is specified.                                                                                                                |
| `getLandmark(landmarkId: Int)`           | Retrieves the landmark with the specified landmarkId from the store. Returns null if the landmark does not exist in the store.                                                                                                                                                                                            |
| `updateLandmark(landmark: UnlLandmark)`     | Updates information about a specific landmark in the store. This does not affect the landmark's category. The landmark must belong to this store.                                                                                                                                                                         |
| `categories`                             | Retrieves a list of all categories in the store.                                                                                                                                                                                                                                                                          |
| `getCategoryById`                        | Fetches a category by its ID. Returns `null` if not found.                                                                                                                                                                                                                                                                |
| `getLandmarks`                           | Retrieves a list of landmarks in a specified category. Defaults to all categories if none is specified.                                                                                                                                                                                                                   |
| `removeCategory`                         | Removes a category by its ID. Optionally removes landmarks in the category or marks them as uncategorized.                                                                                                                                                                                                                |
| `removeLandmark`                         | Removes a specific landmark from the store.                                                                                                                                                                                                                                                                               |
| `updateCategory`                         | Updates a specific category's details. The category must belong to this store.                                                                                                                                                                                                                                            |
| `removeAllLandmarks`                     | Removes all landmarks from the store.                                                                                                                                                                                                                                                                                     |
| `id`                                     | Retrieves the ID of the landmark store.                                                                                                                                                                                                                                                                                   |
| `name`                                   | Retrieves the name of the landmark store.                                                                                                                                                                                                                                                                                 |
| `type`                                   | Retrieves the type of the landmark store. Can be none, defaultType, mapAddress, mapPoi, mapCity, mapHighwayExit or mapCountry.                                                                                                                                                                                            |
| `startFastUpdateMode()`                  | Fast update mode allows fast insert, delete and update operations This mode should be used with caution because if a power failure or process crash interrupts it the database will likely go corrupt and will be deleted at next startup This is intended for fast importing external landmarks into application format. |
| `stopFastUpdateMode()`                   | Stop landmark store fast update mode.                                                                                                                                                                                                                                                                                     |
| `isFastUpdateMode()`                     | Checks if fast update mode is currently enabled. Returns `true` if it is, `false` otherwise.                                                                                                                                                                                                                              |
| `importLandmarks(...)`                   | Imports landmarks from a file into the store. Supports various formats (e.g., KML, GeoJSON). Optionally associates an image with the landmarks. Provides a callback for completion and error handling.                                                                                                                    |
| `importLandmarksBuffer(...)`             | Imports landmarks from a raw data buffer into the store. Supports various formats (e.g., KML, GeoJSON). Optionally associates an image with the landmarks. Provides a callback for completion and error handling.                                                                                                         |
| `cancelImportLandmarks()`                | cancels any ongoing landmark import operation.                                                                                                                                                                                                                                                                            |

### Usage[​](#usage-1 "Direct link to Usage")

* Landmarks are displayed on the map through landmark stores.
* Landmark stores are used to customize search functionality.
* Alarms for approaching landmarks are managed through landmark stores.
* Landmark stores are used for persisting landmarks between different sessions.
