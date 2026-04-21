# Search & Geocoding features

The Navigation SDK for Android provides geocoding and reverse geocoding capabilities. Key features include:

* Reverse Geocoding: Transform geographic coordinates into comprehensive address details, such as country, city, street name, postal code, and more.
* Geocoding: Locate specific places (e.g., cities, streets, or house numbers) based on address components.
* Route-Based Search: Perform searches along predefined routes to identify landmarks and points of interest.
* Wikipedia Integration: Access Wikipedia descriptions and related information for identified landmarks.
* Auto-Suggestion Implementation: Dynamically generate search suggestions for Android UI components.

## Reverse geocode coordinates to address[​](#reverse-geocode-coordinates-to-address "Direct link to Reverse geocode coordinates to address")

Given a coordinate, we can get the corresponding address by searching around the given location and getting the `UnlAddressInfo` associated with the nearest UnlLandmark found nearby. The UnlAddressInfo provides information about the country, city, street name, street number, postal code, state, district, country code, and other relevant information.

Fields from an `UnlAddressInfo` object can be accessed via the `getField` method or can be automatically converted to a string containing the address info using the `format` method.

* Kotlin
* Java

```kotlin
// Kotlin
val preferences = UnlSearchPreferences().apply {
    thresholdDistance = 50
}
val coordinates = UnlCoordinates(51.519305, -0.128022)

val searchService = UnlSearchService(
    preferences = preferences,
    onCompleted = { results, errorCode, hint ->
        when (errorCode) {
            UnlError.NoError -> {
                if (results.isEmpty()) {
                    Log.d("UnlSearchService", "No results found")
                } else {
                    val landmark = results.first()
                    val addressInfo = landmark.addressInfo

                    val country = addressInfo?.getField(EUnlAddressField.Country)
                    val city = addressInfo?.getField(EUnlAddressField.City)
                    val street = addressInfo?.getField(EUnlAddressField.StreetName)
                    val streetNumber = addressInfo?.getField(EUnlAddressField.StreetNumber)

                    val fullAddress = addressInfo?.format()
                    Log.d("UnlSearchService", "Address: $fullAddress")
                }
            }
            else -> {
                Log.e("UnlSearchService", "Error: ${UnlError.getMessage(errorCode)}")
            }
        }
    }
)

val result = searchService.searchAroundPosition(coordinates)

```

```java
// Java
UnlSearchPreferences preferences = new UnlSearchPreferences();
preferences.setThresholdDistance(50);
UnlCoordinates coordinates = new UnlCoordinates(51.519305, -0.128022);

UnlSearchService searchService = new UnlSearchService(
    preferences,
    (results, errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            if (results.isEmpty()) {
                Log.d("UnlSearchService", "No results found");
            } else {
                UnlLandmark landmark = results.get(0);
                UnlAddressInfo addressInfo = landmark.getAddressInfo();

                String country = addressInfo != null ? addressInfo.getField(EUnlAddressField.Country) : null;
                String city = addressInfo != null ? addressInfo.getField(EUnlAddressField.City) : null;
                String street = addressInfo != null ? addressInfo.getField(EUnlAddressField.StreetName) : null;
                String streetNumber = addressInfo != null ? addressInfo.getField(EUnlAddressField.StreetNumber) : null;

                String fullAddress = addressInfo != null ? addressInfo.format() : null;
                Log.d("UnlSearchService", "Address: " + fullAddress);
            }
        } else {
            Log.e("UnlSearchService", "Error: " + UnlError.getMessage(errorCode));
        }
    }
);

int result = searchService.searchAroundPosition(coordinates);

```

## Geocode address to location[​](#geocode-address-to-location "Direct link to Geocode address to location")

The Navigation SDK for Android provides geocoding capabilities to convert addresses into geographic coordinates. Addresses represent a tree-like structure, where each node is a `UnlLandmark` with a specific `EAddressDetailLevel`. At the root of the tree, we have the country-level landmarks, followed by other levels such as cities, streets, and house numbers.

> 🚨 **DANGER**
>
> The address structure is not the same in all countries. For example, some countries do not have states or provinces.
>
> Use the `getNextAddressDetailLevel` method from the `GuidedAddressSearchService` class to get the next available levels in the address hierarchy.

### Search countries by name[​](#search-countries-by-name "Direct link to Search countries by name")

It is possible to do a search at a country level. In this case you can use code like the following:

* Kotlin
* Java

```kotlin
// Kotlin
val guidedSearchService = GuidedAddressSearchService(
    onCompleted = { results, errorCode, hint ->
        when (errorCode) {
            UnlError.NoError, UnlError.ReducedResult -> {
                if (results.isEmpty()) {
                    Log.d("GuidedSearch", "No results")
                } else {
                    // do something with results
                    Log.d("GuidedSearch", "Found ${results.size} countries")
                }
            }
            else -> {
                Log.e("GuidedSearch", "Error: ${UnlError.getMessage(errorCode)}")
            }
        }
    }
)

// Search for countries by name - this will be handled by creating a search method
// that uses the general search functionality for country-level results

```

```java
// Java
GuidedAddressSearchService guidedSearchService = new GuidedAddressSearchService(
    (results, errorCode, hint) -> {
        if (errorCode == UnlError.NoError || errorCode == UnlError.ReducedResult) {
            if (results.isEmpty()) {
                Log.d("GuidedSearch", "No results");
            } else {
                // do something with results
                Log.d("GuidedSearch", "Found " + results.size() + " countries");
            }
        } else {
            Log.e("GuidedSearch", "Error: " + UnlError.getMessage(errorCode));
        }
    }
);

// Search for countries by name - this will be handled by creating a search method
// that uses the general search functionality for country-level results

```

This can provide the parent landmark for the other `GuidedAddressSearchService` methods.

It does a search restricted to country-level results. This allows the use of more flexible search terms as it works regardless of the language.

### Search the hierarchical address structure[​](#search-the-hierarchical-address-structure "Direct link to Search the hierarchical address structure")

We will create the example in two steps. First, we create a function that for a parent landmark, an `EAddressDetailLevel` and a text returns the children having the required detail level and matching the text.

The possible values for `EAddressDetailLevel` are: NoDetail, Country, State, County, District, City, Settlement, PostalCode, Street, StreetSection, StreetLane, StreetAlley, HouseNumber, Crossing.

* Kotlin
* Java

```kotlin
// Kotlin
// Address search method using coroutines
suspend fun searchAddress(
    landmark: UnlLandmark,
    detailLevel: EAddressDetailLevel,
    text: String
): UnlLandmark? = suspendCoroutine { continuation ->
    val guidedSearchService = GuidedAddressSearchService(
        onCompleted = { results, errorCode, hint ->
            when (errorCode) {
                UnlError.NoError, UnlError.ReducedResult -> {
                    if (results.isEmpty()) {
                        continuation.resume(null)
                    } else {
                        continuation.resume(results.first())
                    }
                }
                else -> {
                    continuation.resume(null)
                }
            }
        }
    )

    guidedSearchService.search(landmark, text, detailLevel)
}

```

```java
// Java
// Address search method using CompletableFuture
CompletableFuture<UnlLandmark> searchAddress(
    UnlLandmark landmark,
    EAddressDetailLevel detailLevel,
    String text
) {
    CompletableFuture<UnlLandmark> future = new CompletableFuture<>();
    GuidedAddressSearchService guidedSearchService = new GuidedAddressSearchService(
        (results, errorCode, hint) -> {
            if (errorCode == UnlError.NoError || errorCode == UnlError.ReducedResult) {
                if (results.isEmpty()) {
                    future.complete(null);
                } else {
                    future.complete(results.get(0));
                }
            } else {
                future.complete(null);
            }
        }
    );

    guidedSearchService.search(landmark, text, detailLevel);
    return future;
}

```

Using the function above, we can look for the children landmarks following the conditions:

* Kotlin
* Java

```kotlin
// Kotlin
// Use coroutines in a lifecycle-aware scope
lifecycleScope.launch {
    val guidedSearchService = GuidedAddressSearchService()
    val countryLandmark = guidedSearchService.getCountryLevelItem("ESP")

    if (countryLandmark == null) {
        Log.e("GuidedSearch", "Country not found")
        return@launch
    }

    Log.d("GuidedSearch", "Country: ${countryLandmark.name}")

    // Use the address search to get a landmark for a city in Spain (e.g., Barcelona).
    val cityLandmark = searchAddress(
        landmark = countryLandmark,
        detailLevel = EAddressDetailLevel.City,
        text = "Barcelona"
    )

    if (cityLandmark == null) {
        Log.e("GuidedSearch", "City not found")
        return@launch
    }

    Log.d("GuidedSearch", "City: ${cityLandmark.name}")

    // Use the address search to get a predefined street's landmark in the city (e.g., Carrer de Mallorca).
    val streetLandmark = searchAddress(
        landmark = cityLandmark,
        detailLevel = EAddressDetailLevel.Street,
        text = "Carrer de Mallorca"
    )

    if (streetLandmark == null) {
        Log.e("GuidedSearch", "Street not found")
        return@launch
    }

    Log.d("GuidedSearch", "Street: ${streetLandmark.name}")

    // Use the address search to get a predefined house number's landmark on the street (e.g., House Number 401).
    val houseNumberLandmark = searchAddress(
        landmark = streetLandmark,
        detailLevel = EAddressDetailLevel.HouseNumber,
        text = "401"
    )

    if (houseNumberLandmark == null) {
        Log.e("GuidedSearch", "House number not found")
        return@launch
    }

    Log.d("GuidedSearch", "House number: ${houseNumberLandmark.name}")
}

```

```java
// Java
// Use CompletableFuture chaining
GuidedAddressSearchService guidedSearchService = new GuidedAddressSearchService();
UnlLandmark countryLandmark = guidedSearchService.getCountryLevelItem("ESP");

if (countryLandmark == null) {
    Log.e("GuidedSearch", "Country not found");
    return;
}

Log.d("GuidedSearch", "Country: " + countryLandmark.getName());

// Use the address search to get a landmark for a city in Spain (e.g., Barcelona).
searchAddress(countryLandmark, EAddressDetailLevel.City, "Barcelona")
    .thenAccept(cityLandmark -> {
        if (cityLandmark == null) {
            Log.e("GuidedSearch", "City not found");
            return;
        }

        Log.d("GuidedSearch", "City: " + cityLandmark.getName());

        // Use the address search to get a predefined street's landmark in the city (e.g., Carrer de Mallorca).
        searchAddress(cityLandmark, EAddressDetailLevel.Street, "Carrer de Mallorca")
            .thenAccept(streetLandmark -> {
                if (streetLandmark == null) {
                    Log.e("GuidedSearch", "Street not found");
                    return;
                }

                Log.d("GuidedSearch", "Street: " + streetLandmark.getName());

                // Use the address search to get a predefined house number's landmark on the street (e.g., House Number 401).
                searchAddress(streetLandmark, EAddressDetailLevel.HouseNumber, "401")
                    .thenAccept(houseNumberLandmark -> {
                        if (houseNumberLandmark == null) {
                            Log.e("GuidedSearch", "House number not found");
                            return;
                        }

                        Log.d("GuidedSearch", "House number: " + houseNumberLandmark.getName());
                    });
            });
    });

```

The `getCountryLevelItem` method returns the root node corresponding to the specified country based on the provided country code. If the country code is invalid, the function will return null.

## Geocode location to Wikipedia[​](#geocode-location-to-wikipedia "Direct link to Geocode location to Wikipedia")

A useful functionality when looking for something, is to obtain the content of a Wikipedia page for a specific text filter. This can be done by a normal search, followed by a call to `ExternalInfo.requestWikiInfo`.

* Kotlin
* Java

```kotlin
// Kotlin
val externalInfo = ExternalInfo()
val progressListener = object : UnlProgressListener() {
    override fun notifyComplete(errorCode: ErrorCode, hint: String) {
        when (errorCode) {
            UnlError.NoError -> {
                val title = externalInfo.wikiPageTitle
                val description = externalInfo.wikiPageDescription
                val url = externalInfo.wikiPageURL

                Log.d("Wikipedia", "Title: $title")
                Log.d("Wikipedia", "Description: $description")
                Log.d("Wikipedia", "URL: $url")
            }
            else -> {
                Log.e("Wikipedia", "Error getting Wikipedia info: ${UnlError.getMessage(errorCode)}")
            }
        }
    }
}

// Check if landmark has Wikipedia info and request it
if (externalInfo.hasWikiInfo(landmark)) {
    externalInfo.requestWikiInfo(landmark, progressListener)
} else {
    Log.d("Wikipedia", "No Wikipedia info available for this landmark")
}

```

```java
// Java
ExternalInfo externalInfo = new ExternalInfo();
UnlProgressListener progressListener = new UnlProgressListener() {
    @Override
    public void notifyComplete(int errorCode, String hint) {
        if (errorCode == UnlError.NoError) {
            String title = externalInfo.getWikiPageTitle();
            String description = externalInfo.getWikiPageDescription();
            String url = externalInfo.getWikiPageURL();

            Log.d("Wikipedia", "Title: " + title);
            Log.d("Wikipedia", "Description: " + description);
            Log.d("Wikipedia", "URL: " + url);
        } else {
            Log.e("Wikipedia", "Error getting Wikipedia info: " + UnlError.getMessage(errorCode));
        }
    }
};

// Check if landmark has Wikipedia info and request it
if (externalInfo.hasWikiInfo(landmark)) {
    externalInfo.requestWikiInfo(landmark, progressListener);
} else {
    Log.d("Wikipedia", "No Wikipedia info available for this landmark");
}

```

See the [Location Wikipedia guide](../01-Overview/04-Todo.md) for more info.

## Get auto-suggestions[​](#get-auto-suggestions "Direct link to Get auto-suggestions")

Auto-suggestions can be implemented by calling `UnlSearchService.searchByFilter` with the text filter set to the current text from the Android search widget when the field value is changed. A simple example is demonstrated below:

* Kotlin
* Java

```kotlin
// Kotlin
class SearchActivity : AppCompatActivity() {
    private lateinit var searchView: SearchView
    private var currentSearchService: UnlSearchService? = null
    private val searchHandler = Handler(Looper.getMainLooper())
    private var searchRunnable: Runnable? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        searchView = findViewById(R.id.search_view)
        setupSearchView()
    }

    private fun setupSearchView() {
        searchView.setOnQueryTextListener(object : SearchView.OnQueryTextListener {
            override fun onQueryTextSubmit(query: String?): Boolean {
                // Handle search submission
                return true
            }

            override fun onQueryTextChange(newText: String?): Boolean {
                // Cancel previous search and debounce new searches
                searchRunnable?.let { searchHandler.removeCallbacks(it) }

                searchRunnable = Runnable {
                    getAutoSuggestions(newText ?: "")
                }

                searchHandler.postDelayed(searchRunnable!!, 300) // 300ms debounce
                return true
            }
        })
    }

    private fun getAutoSuggestions(query: String) {
        if (query.isEmpty()) return

        Log.d("AutoSuggestion", "New auto suggestion search for: $query")

        // Cancel previous search
        currentSearchService?.cancelSearch()

        val refCoordinates = UnlCoordinates(48.0, 2.0) // Or use current position
        val preferences = UnlSearchPreferences().apply {
            allowFuzzyResults = true
            maxMatches = 10
        }

        currentSearchService = UnlSearchService(
            preferences = preferences,
            onCompleted = { results, errorCode, hint ->
                when (errorCode) {
                    UnlError.NoError -> {
                        displaySuggestions(results)
                        Log.d("AutoSuggestion", "Got ${results.size} results for: $query")
                    }
                    UnlError.Cancel -> {
                        Log.d("AutoSuggestion", "Search cancelled for: $query")
                    }
                    else -> {
                        Log.e("AutoSuggestion", "Search error for $query: ${UnlError.getMessage(errorCode)}")
                    }
                }
            }
        )

        currentSearchService?.searchByFilter(query, refCoordinates)
    }

    private fun displaySuggestions(landmarks: UnlLandmarkList) {
        // Update your RecyclerView adapter or suggestion list here
        // This is where you would populate your suggestions UI
        for (landmark in landmarks) {
            Log.d("Suggestion", "UnlLandmark: ${landmark.name}")
            // Add to suggestions adapter
        }
    }
}

```

```java
// Java
public class SearchActivity extends AppCompatActivity {
    private SearchView searchView;
    private UnlSearchService currentSearchService;
    private final Handler searchHandler = new Handler(Looper.getMainLooper());
    private Runnable searchRunnable;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        searchView = findViewById(R.id.search_view);
        setupSearchView();
    }

    private void setupSearchView() {
        searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
            @Override
            public boolean onQueryTextSubmit(String query) {
                // Handle search submission
                return true;
            }

            @Override
            public boolean onQueryTextChange(String newText) {
                // Cancel previous search and debounce new searches
                if (searchRunnable != null) {
                    searchHandler.removeCallbacks(searchRunnable);
                }

                searchRunnable = () -> getAutoSuggestions(newText != null ? newText : "");

                searchHandler.postDelayed(searchRunnable, 300); // 300ms debounce
                return true;
            }
        });
    }

    private void getAutoSuggestions(String query) {
        if (query.isEmpty()) return;

        Log.d("AutoSuggestion", "New auto suggestion search for: " + query);

        // Cancel previous search
        if (currentSearchService != null) {
            currentSearchService.cancelSearch();
        }

        UnlCoordinates refCoordinates = new UnlCoordinates(48.0, 2.0); // Or use current position
        UnlSearchPreferences preferences = new UnlSearchPreferences();
        preferences.setAllowFuzzyResults(true);
        preferences.setMaxMatches(10);

        currentSearchService = new UnlSearchService(
            preferences,
            (results, errorCode, hint) -> {
                if (errorCode == UnlError.NoError) {
                    displaySuggestions(results);
                    Log.d("AutoSuggestion", "Got " + results.size() + " results for: " + query);
                } else if (errorCode == UnlError.Cancel) {
                    Log.d("AutoSuggestion", "Search cancelled for: " + query);
                } else {
                    Log.e("AutoSuggestion", "Search error for " + query + ": " + UnlError.getMessage(errorCode));
                }
            }
        );

        currentSearchService.searchByFilter(query, refCoordinates);
    }

    private void displaySuggestions(UnlLandmarkList landmarks) {
        // Update your RecyclerView adapter or suggestion list here
        // This is where you would populate your suggestions UI
        for (UnlLandmark landmark : landmarks) {
            Log.d("Suggestion", "UnlLandmark: " + landmark.getName());
            // Add to suggestions adapter
        }
    }
}

```

The preferences with `allowFuzzyResults` set to true allows for partial match during search. The `refCoordinates` might be replaced with a more suitable value, such as the user current position or the map viewport center.

## Search along a route[​](#search-along-a-route "Direct link to Search along a route")

It is possible also to do a search along a route, not based on some coordinates. In this case you can use code like the following:

* Kotlin
* Java

```kotlin
// Kotlin
val searchService = UnlSearchService(
    onCompleted = { results, errorCode, hint ->
        when (errorCode) {
            UnlError.NoError -> {
                if (results.isEmpty()) {
                    Log.d("RouteSearch", "No results")
                } else {
                    Log.d("RouteSearch", "Results size: ${results.size}")
                    for (landmark in results) {
                        // do something with landmarks
                        Log.d("RouteSearch", "Found: ${landmark.name}")
                    }
                }
            }
            else -> {
                Log.e("RouteSearch", "No results found: ${UnlError.getMessage(errorCode)}")
            }
        }
    }
)

val result = searchService.searchAlongRoute(route)

```

```java
// Java
UnlSearchService searchService = new UnlSearchService(
    (results, errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            if (results.isEmpty()) {
                Log.d("RouteSearch", "No results");
            } else {
                Log.d("RouteSearch", "Results size: " + results.size());
                for (UnlLandmark landmark : results) {
                    // do something with landmarks
                    Log.d("RouteSearch", "Found: " + landmark.getName());
                }
            }
        } else {
            Log.e("RouteSearch", "No results found: " + UnlError.getMessage(errorCode));
        }
    }
);

int result = searchService.searchAlongRoute(route);

```

We can set a custom value for `UnlSearchPreferences.thresholdDistance` in order to specify the maximum distance to the route for the landmarks to be searched. Other `UnlSearchPreferences` fields can be specified depending on the usecase.
