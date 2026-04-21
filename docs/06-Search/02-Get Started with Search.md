# Getting started with Search

The Navigation SDK for Android provides a flexible and robust search functionality, allowing the search of locations using text queries and coordinates:

* Text Search: Perform searches using a text query and geographic coordinates to prioritize results within a specific area.
* Search Preferences: Customize search behavior using various options, such as allowing fuzzy results, limiting search distance, or specifying the number of results.
* Category-Based Search: Filter search results by predefined categories, such as gas stations or parking areas.
* Proximity Search: Retrieve all nearby landmarks without specifying a text query.

## Text search[​](#text-search "Direct link to Text search")

The simplest way to search for something is by providing text and specifying coordinates. The coordinates serve as a hint, prioritizing points of interest (POIs) within the indicated area.

* Kotlin
* Java

```kotlin
// Kotlin
val textFilter = "Paris"
val reference = UnlCoordinates(45.0, 10.0)
val preferences = UnlSearchPreferences().apply {
    maxMatches = 40
    allowFuzzyResults = true
}

val searchService = UnlSearchService(
    preferences = preferences,
    onCompleted = { results, errorCode, hint ->
        // If there is an error or there aren't any results, the method will return an empty list.
        when (errorCode) {
            UnlError.NoError -> {
                if (results.isEmpty()) {
                    Log.d("UnlSearchService", "No results")
                } else {
                    Log.d("UnlSearchService", "Number of results: ${results.size}")
                }
            }
            else -> {
                Log.e("UnlSearchService", "Error: ${UnlError.getMessage(errorCode)}")
            }
        }
    }
)

val result = searchService.searchByFilter(textFilter, reference)

```

```java
// Java
String textFilter = "Paris";
UnlCoordinates reference = new UnlCoordinates(45.0, 10.0);
UnlSearchPreferences preferences = new UnlSearchPreferences();
preferences.setMaxMatches(40);
preferences.setAllowFuzzyResults(true);

UnlSearchService searchService = new UnlSearchService(
    preferences,
    (results, errorCode, hint) -> {
        // If there is an error or there aren't any results, the method will return an empty list.
        if (errorCode == UnlError.NoError) {
            if (results.isEmpty()) {
                Log.d("UnlSearchService", "No results");
            } else {
                Log.d("UnlSearchService", "Number of results: " + results.size());
            }
        } else {
            Log.e("UnlSearchService", "Error: " + UnlError.getMessage(errorCode));
        }
    }
);

int result = searchService.searchByFilter(textFilter, reference);

```

> 📝 **INFO**
>
> The `searchByFilter` method returns an error code. If the search fails to initialize, the error details will be delivered through the `onCompleted` callback function.

The `errorCode` provided by the callback function can have the following values:

| Value                       |Significance              |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `UnlError.NoError`          | successfully completed                                                                                                                                       |
| `UnlError.Cancel`           | cancelled by the user                                                                                                                                        |
| `UnlError.NoMemory`         | search engine couldn't allocate the necessary memory for the operation                                                                                       |
| `UnlError.OperationTimeout` | search was executed on the online service and the operation took too much time to complete (usually more than 1 min, depending on the server overload state) |
| `UnlError.NetworkTimeout`   | can't establish the connection or the server didn't respond on time                                                                                          |
| `UnlError.NetworkFailed`    | search was executed on the online service and the operation failed due to bad network connection                                                             |

## Specifying preferences[​](#specifying-preferences "Direct link to Specifying preferences")

As seen in the previous example, before searching we need to specify some `UnlSearchPreferences`. The following characteristics apply to a search:

| Field                  | Type    | Default Value | Explanation                                                                          |
| ---------------------- | ------- | ------------- | ------------------------------------------------------------------------------------ |
| allowFuzzyResults      | Boolean | true          | Allows fuzzy search results, enabling approximate matches for queries.               |
| exactMatchEnabled      | Boolean | false         | Restricts results to only those that exactly match the query.                        |
| maxMatches             | Int     | 40            | Specifies the maximum number of search results to return.                            |
| searchAddressesEnabled | Boolean | true          | Includes addresses in the search results. This option also includes roads.           |
| searchMapPOIsEnabled   | Boolean | true          | Includes points of interest (POIs) on the map in the search results.                 |
| searchOnlyOnboard      | Boolean | false         | Limits the search to onboard (offline) data only.                                    |
| thresholdDistance      | Int     | 2147483647    | Defines the maximum distance (in meters) for search results from the query location. |

### Search by category[​](#search-by-category "Direct link to Search by category")

The Navigation SDK for Android allows the user to filter results based on the category. Some predefined categories are available and can be accessed using `EGenericCategoriesIDs`.

In the following example, we perform a search for a text query, limiting the results to gas stations:

* Kotlin
* Java

```kotlin
// Kotlin
val textFilter = "Paris"
val reference = UnlCoordinates(45.0, 10.0)
val preferences = UnlSearchPreferences().apply {
    maxMatches = 40
    allowFuzzyResults = true
    searchMapPOIsEnabled = true
    searchAddressesEnabled = false
}

val searchService = UnlSearchService(
    preferences = preferences,
    onCompleted = { results, errorCode, hint ->
        when (errorCode) {
            UnlError.NoError -> {
                if (results.isEmpty()) {
                    Log.d("UnlSearchService", "No results")
                } else {
                    Log.d("UnlSearchService", "Number of results: ${results.size}")
                }
            }
            else -> {
                Log.e("UnlSearchService", "Error: ${UnlError.getMessage(errorCode)}")
            }
        }
    }
)

val result = searchService.searchByFilter(
    textFilter,
    reference,
    arrayListOf(EGenericCategoriesIDs.GasStation)
)

```

```java
// Java
String textFilter = "Paris";
UnlCoordinates reference = new UnlCoordinates(45.0, 10.0);
UnlSearchPreferences preferences = new UnlSearchPreferences();
preferences.setMaxMatches(40);
preferences.setAllowFuzzyResults(true);
preferences.setSearchMapPOIsEnabled(true);
preferences.setSearchAddressesEnabled(false);

UnlSearchService searchService = new UnlSearchService(
    preferences,
    (results, errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            if (results.isEmpty()) {
                Log.d("UnlSearchService", "No results");
            } else {
                Log.d("UnlSearchService", "Number of results: " + results.size());
            }
        } else {
            Log.e("UnlSearchService", "Error: " + UnlError.getMessage(errorCode));
        }
    }
);

ArrayList<EGenericCategoriesIDs> categories = new ArrayList<>();
categories.add(EGenericCategoriesIDs.GasStation);
int result = searchService.searchByFilter(textFilter, reference, categories);

```

> 💡 **TIP**
>
> Set the `searchAddressesEnabled` to false in order to filter non-relevant results.

### Search on custom landmarks[​](#search-on-custom-landmarks "Direct link to Search on custom landmarks")

By default all search methods operate on the landmarks provided by default on the map. You can enable search functionality for custom landmarks by creating a landmark store containing the desired landmarks and adding it to the search preferences.

* Kotlin
* Java

```kotlin
// Kotlin
// Create the landmarks to be added
val landmark1 = UnlLandmark("My Custom Landmark1", UnlCoordinates(25.0, 30.0))
val landmark2 = UnlLandmark("My Custom Landmark2", UnlCoordinates(25.005, 30.005))

// Create a store and add the landmarks
val store = UnlLandmarkStoreService.createLandmarkStore("LandmarksToBeSearched")
store.addLandmark(landmark1)
store.addLandmark(landmark2)

// Add the store to the search preferences
val preferences = UnlSearchPreferences().apply {
    landmarkStores?.add(store)
    // If no results from the map POIs should be returned then searchMapPOIsEnabled should be set to false
    searchMapPOIsEnabled = false
    // If no results from the addresses should be returned then searchAddressesEnabled should be set to false
    searchAddressesEnabled = false
}

val searchService = UnlSearchService(
    preferences = preferences,
    onCompleted = { results, errorCode, hint ->
        when (errorCode) {
            UnlError.NoError -> {
                if (results.isEmpty()) {
                    Log.d("UnlSearchService", "No results")
                } else {
                    Log.d("UnlSearchService", "Number of results: ${results.size}")
                }
            }
            else -> {
                Log.e("UnlSearchService", "Error: ${UnlError.getMessage(errorCode)}")
            }
        }
    }
)

val result = searchService.searchByFilter(
    "My Custom UnlLandmark",
    UnlCoordinates(25.003, 30.003)
)

```

```java
// Java
// Create the landmarks to be added
UnlLandmark landmark1 = new UnlLandmark("My Custom Landmark1", new UnlCoordinates(25.0, 30.0));
UnlLandmark landmark2 = new UnlLandmark("My Custom Landmark2", new UnlCoordinates(25.005, 30.005));

// Create a store and add the landmarks
UnlLandmarkStore store = UnlLandmarkStoreService.createLandmarkStore("LandmarksToBeSearched");
store.addLandmark(landmark1);
store.addLandmark(landmark2);

// Add the store to the search preferences
UnlSearchPreferences preferences = new UnlSearchPreferences();
if (preferences.getLandmarkStores() != null) {
    preferences.getLandmarkStores().add(store);
}
// If no results from the map POIs should be returned then searchMapPOIsEnabled should be set to false
preferences.setSearchMapPOIsEnabled(false);
// If no results from the addresses should be returned then searchAddressesEnabled should be set to false
preferences.setSearchAddressesEnabled(false);

UnlSearchService searchService = new UnlSearchService(
    preferences,
    (results, errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            if (results.isEmpty()) {
                Log.d("UnlSearchService", "No results");
            } else {
                Log.d("UnlSearchService", "Number of results: " + results.size());
            }
        } else {
            Log.e("UnlSearchService", "Error: " + UnlError.getMessage(errorCode));
        }
    }
);

int result = searchService.searchByFilter(
    "My Custom UnlLandmark",
    new UnlCoordinates(25.003, 30.003)
);

```

> 🚨 **DANGER**
>
> The landmark store **retains** the landmarks added to it across sessions, until the app is **uninstalled**. This means a previously created landmark store with the same name might already exist in persistent storage and may contain pre-existing landmarks. For more details, refer to the [documentation on UnlLandmarkStore](../03-Core/04-Landmarks.md#landmark-stores).

> 💡 **TIP**
>
> Set the `searchAddressesEnabled` and `searchMapPOIsEnabled` to false in order to filter non-relevant results.

### Search on overlays[​](#search-on-overlays "Direct link to Search on overlays")

You can perform searches on overlays by specifying the overlay ID. It is recommended to consult the [Overlay documentation](../03-Core/06-Overlays.md) for a deeper understanding and details about proper usage.

In the example below, we demonstrate how to search within items from the safety overlay. Custom overlays can also be used, provided they are activated in the applied map style:

* Kotlin
* Java

```kotlin
// Kotlin
// Get the overlay id of safety
val overlayId = ECommonOverlayIds.Safety.value

// Add the overlay to the search preferences
val preferences = UnlSearchPreferences().apply {
    // We can set searchMapPOIsEnabled and searchAddressesEnabled to false if no results from the map POIs and addresses should be returned
    searchMapPOIsEnabled = false
    searchAddressesEnabled = false
}

val searchService = UnlSearchService(
    preferences = preferences,
    onCompleted = { results, errorCode, hint ->
        when (errorCode) {
            UnlError.NoError -> {
                if (results.isEmpty()) {
                    Log.d("UnlSearchService", "No results")
                } else {
                    val mapView: UnlMapView? = findViewById<UnlMapSurfaceView>(R.id.gem_surface).mapview
                    mapView?.centerOnCoordinates(results.first().coordinates)
                    Log.d("UnlSearchService", "Number of results: ${results.size}")
                }
            }
            else -> {
                Log.e("UnlSearchService", "Error: ${UnlError.getMessage(errorCode)}")
            }
        }
    }
)

val result = searchService.searchByFilter(
    "Speed",
    UnlCoordinates(48.76930, 2.34483)
)

```

```java
// Java
// Get the overlay id of safety
int overlayId = ECommonOverlayIds.Safety.getValue();

// Add the overlay to the search preferences
UnlSearchPreferences preferences = new UnlSearchPreferences();
// We can set searchMapPOIsEnabled and searchAddressesEnabled to false if no results from the map POIs and addresses should be returned
preferences.setSearchMapPOIsEnabled(false);
preferences.setSearchAddressesEnabled(false);

UnlSearchService searchService = new UnlSearchService(
    preferences,
    (results, errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            if (results.isEmpty()) {
                Log.d("UnlSearchService", "No results");
            } else {
                UnlMapView mapView = findViewById(R.id.gem_surface).getMapview();
                if (mapView != null) {
                    mapView.centerOnCoordinates(results.get(0).getCoordinates());
                }
                Log.d("UnlSearchService", "Number of results: " + results.size());
            }
        } else {
            Log.e("UnlSearchService", "Error: " + UnlError.getMessage(errorCode));
        }
    }
);

int result = searchService.searchByFilter(
    "Speed",
    new UnlCoordinates(48.76930, 2.34483)
);

```

In order to convert the returned `UnlLandmark` to a `OverlayItem` use the `overlayItem` property of the `UnlLandmark` class. This property returns the associated `OverlayItem` if available, null otherwise.

> 💡 **TIP**
>
> Set the `searchAddressesEnabled` and `searchMapPOIsEnabled` to false in order to filter non-relevant results.

## Search for location[​](#search-for-location "Direct link to Search for location")

If you don't specify any text, all the landmarks in the closest proximity are returned, limited to `maxMatches`.

* Kotlin
* Java

```kotlin
// Kotlin
val reference = UnlCoordinates(45.0, 10.0)
val preferences = UnlSearchPreferences().apply {
    maxMatches = 40
    allowFuzzyResults = true
}

val searchService = UnlSearchService(
    preferences = preferences,
    onCompleted = { results, errorCode, hint ->
        // If there is an error or there aren't any results, the method will return an empty list.
        when (errorCode) {
            UnlError.NoError -> {
                if (results.isEmpty()) {
                    Log.d("UnlSearchService", "No results")
                } else {
                    Log.d("UnlSearchService", "Number of results: ${results.size}")
                }
            }
            else -> {
                Log.e("UnlSearchService", "Error: ${UnlError.getMessage(errorCode)}")
            }
        }
    }
)

val result = searchService.searchAroundPosition(reference)

```

```java
// Java
UnlCoordinates reference = new UnlCoordinates(45.0, 10.0);
UnlSearchPreferences preferences = new UnlSearchPreferences();
preferences.setMaxMatches(40);
preferences.setAllowFuzzyResults(true);

UnlSearchService searchService = new UnlSearchService(
    preferences,
    (results, errorCode, hint) -> {
        // If there is an error or there aren't any results, the method will return an empty list.
        if (errorCode == UnlError.NoError) {
            if (results.isEmpty()) {
                Log.d("UnlSearchService", "No results");
            } else {
                Log.d("UnlSearchService", "Number of results: " + results.size());
            }
        } else {
            Log.e("UnlSearchService", "Error: " + UnlError.getMessage(errorCode));
        }
    }
);

int result = searchService.searchAroundPosition(reference);

```

To limit the search to a specific area, provide a `RectangleGeographicArea` to the optional `locationHint` parameter.

* Kotlin
* Java

```kotlin
// Kotlin
val reference = UnlCoordinates(41.68905, -72.64296)
val searchArea = RectangleGeographicArea(
    UnlCoordinates(41.98846, -73.12412), // topLeft
    UnlCoordinates(41.37716, -72.02342)  // bottomRight
)

val searchService = UnlSearchService(
    preferences = UnlSearchPreferences().apply { maxMatches = 400 },
    locationHint = searchArea,
    onCompleted = { results, errorCode, hint ->
        // Handle results
    }
)

val result = searchService.searchByFilter("N", reference)

```

```java
// Java
UnlCoordinates reference = new UnlCoordinates(41.68905, -72.64296);
RectangleGeographicArea searchArea = new RectangleGeographicArea(
    new UnlCoordinates(41.98846, -73.12412), // topLeft
    new UnlCoordinates(41.37716, -72.02342)  // bottomRight
);

UnlSearchPreferences preferences = new UnlSearchPreferences();
preferences.setMaxMatches(400);

UnlSearchService searchService = new UnlSearchService(
    preferences,
    searchArea,
    (results, errorCode, hint) -> {
        // Handle results
    }
);

int result = searchService.searchByFilter("N", reference);

```

> 🚨 **DANGER**
>
> The reference coordinates used for search must be located within the `RectangleGeographicArea` provided to the `locationHint` parameter. Otherwise, the search will return an empty list.

## Show the results on the map[​](#show-the-results-on-the-map "Direct link to Show the results on the map")

In most use cases the landmarks found by search are already present on the map. If the search was made on custom landmark stores see the [add map landmarks](../04-Maps/05-Display%20Map%20Items/02-Display%20Landmarks.md#display-custom-landmarks).

To zoom to a landmark found via search, we can use `UnlMapView.centerOnCoordinates()` on the coordinates of the landmark found (`UnlLandmark.coordinates`). See the documentation for [map centering](../04-Maps/03-Adjust%20the%20Map%20View.md#map-centering) for more info.

## Change the language of the results[​](#change-the-language-of-the-results "Direct link to Change the language of the results")

The language of search results and category names is determined by the `UnlSdkSettings.language` setting. Check the [the internationalization guide](../01-Overview/04-Todo.md) section for more details.
