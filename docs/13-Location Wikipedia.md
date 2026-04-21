# Location Wikipedia

Landmarks can include Wikipedia data such as title, image title, URL, description, page summary, language, and more. To demonstrate how to retrieve Wikipedia information, we introduce the `ExternalInfo` class, which handles Wikipedia data.

The `ExternalInfo` class is part of the core SDK and can be instantiated directly to access external information services.

## Check if Wikipedia data is available[​](#check-if-wikipedia-data-is-available "Direct link to Check if Wikipedia data is available")

Use the `hasWikiInfo` method of the `ExternalInfo` class to check if a landmark has Wikipedia data available:

* Kotlin
* Java

```kotlin
// Kotlin
val externalInfoService = ExternalInfo()
val hasExternalInfo = externalInfoService.hasWikiInfo(landmark)

```

```java
//Java
ExternalInfo externalInfoService = new ExternalInfo();
boolean hasExternalInfo = externalInfoService.hasWikiInfo(landmark);

```

> 🚨 **DANGER**
>
> Make sure the Wikipedia related fields from the `extraInfo` property of the `UnlLandmark` object are not tampered with if changes are made to the landmark data.

## ExternalInfo class[​](#externalinfo-class "Direct link to ExternalInfo class")

This class provides Wikipedia information for a landmark. Create an instance of `ExternalInfo` and use the `requestWikiInfo` method to retrieve Wikipedia data.

* Kotlin
* Java

```kotlin
// Kotlin
val externalInfoService = ExternalInfo()

val wikipediaProgressListener = UnlProgressListener.create(
    onStarted = {
        // Show loading indicator
        progressBar.visibility = View.VISIBLE
    },

    onCompleted = { errorCode, _ ->
        progressBar.visibility = View.GONE

        if (errorCode == UnlError.NoError) {
            // Success - access Wikipedia data
            SdkCall.execute {
                val title = externalInfoService.wikiPageTitle
                val content = externalInfoService.wikiPageDescription
                val language = externalInfoService.wikiPageLanguage
                val pageUrl = externalInfoService.wikiPageURL

                // Use the retrieved data
                displayWikipediaInfo(title, content, language, pageUrl)
            }
        } else {
            // Handle error
            showDialog("Error getting wiki info: ${UnlError.getMessage(errorCode)}")
        }
    },

    postOnMain = true
)

SdkCall.execute {
    externalInfoService.requestWikiInfo(landmark, wikipediaProgressListener)
}

```

```java
// Java
ExternalInfo externalInfoService = new ExternalInfo();

UnlProgressListener wikipediaProgressListener = UnlProgressListener.create(
    () -> {
        // Show loading indicator
        progressBar.setVisibility(View.VISIBLE);
    },
    (errorCode, hint) -> {
        progressBar.setVisibility(View.GONE);

        if (errorCode == UnlError.NoError) {
            // Success - access Wikipedia data
            SdkCall.execute(() -> {
                String title = externalInfoService.getWikiPageTitle();
                String content = externalInfoService.getWikiPageDescription();
                String language = externalInfoService.getWikiPageLanguage();
                String pageUrl = externalInfoService.getWikiPageURL();

                // Use the retrieved data
                displayWikipediaInfo(title, content, language, pageUrl);
            });
        } else {
            // Handle error
            showDialog("Error getting wiki info: " + UnlError.getMessage(errorCode));
        }
    },
    true
);

SdkCall.execute(() -> {
    externalInfoService.requestWikiInfo(landmark, wikipediaProgressListener);
});

```

The `requestWikiInfo` method triggers a progress listener that will be notified when the operation completes. You can cancel the request using the `cancelWikiInfo` method.

> 📝 **INFO**
>
> Wikipedia data is provided in the language specified in `UnlSdkSettings`.

The method provides a result based on the outcome of the operation:

* On success returns `UnlError.NoError` and the Wikipedia data is available through the `ExternalInfo` properties.

* On failure returns one of the following `UnlError` values:

  <!-- -->

  * `UnlError.InvalidInput` : The specified landmark does not contain Wikipedia-related information.
  * `UnlError.Connection` : No internet connection is available.
  * `UnlError.NotFound` : Wikipedia information could not be retrieved for the given landmark.
  * `UnlError.General` : An unspecified error occurred.

## Wikipedia image data[​](#wikipedia-image-data "Direct link to Wikipedia image data")

The `ExternalInfo` class provides the following details regarding images:

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
    val imgCount = externalInfoService.wikiImagesCount
    val imageUrl = externalInfoService.getWikiImageURL(0)
    val imageDescription = externalInfoService.getWikiImageDescription(0)
    val imageTitle = externalInfoService.getWikiImageTitle(0)
}

```

```java
// Java
SdkCall.execute(() -> {
    int imgCount = externalInfoService.getWikiImagesCount();
    String imageUrl = externalInfoService.getWikiImageURL(0);
    String imageDescription = externalInfoService.getWikiImageDescription(0);
    String imageTitle = externalInfoService.getWikiImageTitle(0);
});

```

### Requesting Wikipedia image content[​](#requesting-wikipedia-image-content "Direct link to Requesting Wikipedia image content")

You can request the actual image content using the `requestWikiImage` method:

* Kotlin
* Java

```kotlin
// Kotlin
val image = UnlImage()
val externalInfoService = ExternalInfo()
val imageProgressListener = UnlProgressListener.create(
    onCompleted = { errorCode, _ ->
        if (errorCode == UnlError.NoError) {
            // UnlImage loaded successfully
            SdkCall.execute {
                //val bitmap = image.asBitmap(width, height)
                // Display the image
                //imageView.setImageBitmap(bitmap)
            }
        }
    },
    postOnMain = true
)
externalInfoService.requestWikiImage(
    imageProgressListener,
    image,
    nImageIdx = 0,
    EExternalImageQuality.Medium
)

```

```java
// Java
UnlImage image = new UnlImage();
ExternalInfo externalInfoService = new ExternalInfo();
UnlProgressListener imageProgressListener = UnlProgressListener.create(
    (errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            // UnlImage loaded successfully
            SdkCall.execute(() -> {
                //Bitmap bitmap = image.asBitmap(width, height);
                // Display the image
                //imageView.setImageBitmap(bitmap);
            });
        }
    },
    true
);
externalInfoService.requestWikiImage(
    imageProgressListener,
    image,
    0,
    EExternalImageQuality.Medium
);

```

### Wikipedia image information[​](#wikipedia-image-information "Direct link to Wikipedia image information")

Detailed information about an image can be retrieved using the `requestWikiImageInfo` method:

* Kotlin
* Java

```kotlin
// Kotlin
val externalInfoService = ExternalInfo()
val result = GemString()
val imageInfoListener = UnlProgressListener.create(
    onCompleted = { errorCode, _ ->
        if (errorCode == UnlError.NoError) {
            // UnlImage info retrieved successfully
            val imageInfo = result.value// GemString result
            // Process the image information
        } else {
            showDialog("Error getting wiki image info: ${UnlError.getMessage(errorCode)}")
        }
    },
    postOnMain = true
)
externalInfoService.requestWikiImageInfo(imageInfoListener, nImageIdx = 0, result)

```

```java
// Java
ExternalInfo externalInfoService = new ExternalInfo();
GemString result = new GemString();
UnlProgressListener imageInfoListener = UnlProgressListener.create(
    (errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            // UnlImage info retrieved successfully
            String imageInfo = result.getValue(); // GemString result
            // Process the image information
        } else {
            showDialog("Error getting wiki image info: " + UnlError.getMessage(errorCode));
        }
    },
    true
);
externalInfoService.requestWikiImageInfo(imageInfoListener, 0, result);

```

### Canceling requests[​](#canceling-requests "Direct link to Canceling requests")

You can cancel various requests using the available cancel methods:

* Kotlin
* Java

```kotlin
// Kotlin
// Cancel main Wikipedia info request
externalInfoService.cancelWikiInfo()

// Cancel specific image content request
externalInfoService.cancelWikiImageContentRequest(imageProgressListener)

// Cancel specific image info request
externalInfoService.cancelWikiImageInfoRequest(imageInfoListener)

```

```java
//Java
// Cancel main Wikipedia info request
externalInfoService.cancelWikiInfo();

// Cancel specific image content request
externalInfoService.cancelWikiImageContentRequest(imageProgressListener);

// Cancel specific image info request
externalInfoService.cancelWikiImageInfoRequest(imageInfoListener);

```
