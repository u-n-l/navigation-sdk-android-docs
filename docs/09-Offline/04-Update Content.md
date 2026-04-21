# Update Content

The UNL SDK allows updating downloaded content to stay synchronized with the latest map data. New map versions are released every few weeks. The update operation supports the `RoadMap`, `ViewStyleLowRes`, and `ViewStyleHighRes` content types. This article focuses on the `RoadMap` type, as it is the most common use case. Updating styles mostly follows the same process.

The SDK requires all road map content store items to have the same version. It is not possible to have multiple `RoadMap` items with different versions, meaning partial updates of individual items are not supported.

Based on the client's content version relative to the newest available release, it can be in one of three states, as defined by the `EOffboardListenerStatus` enum:

| Status        | Description                                  |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ExpiredData` | The client version is significantly outdated and no longer supports online operations.<br /><br />All features - such as search, route calculation, and navigation - will function exclusively on the device using downloaded regions, even if an internet connection is available. If an operation like route calculation is attempted in non-downloaded regions, it will fail.<br /><br />An update is **mandatory** to restore online functionality. Only relevant for `EContentType.RoadMap` elements. |
| `OldData`     | The client version is outdated but still supports online operations.<br /><br />Features will work online when a connection is available and offline when not. While offline, only downloaded regions are accessible, but online access allows operations across the entire map.<br /><br />An update is **recommended** as soon as possible. Relevant for all types of content store elements.                                                                                                            |
| `UpToDate`    | The client version has the latest map data. Features function online when connected and offline using downloaded regions.<br /><br />No updates are available. Relevant for all types of content store elements.                                                                                                                                                                                                                                                                                           |

The UNL servers support online operations for the most up-to-date version (`EOffboardListenerStatus.UpToDate`) and the previous version (`EOffboardListenerStatus.OldData`) of road map data.

> 📝 **INFO**
>
> After installation, the app includes a default map version of type `ExpiredData`, which contains no available content. Therefore, an update - either manually triggered or performed automatically - is required before the app can be used. Internet access is required for the initial use of the app.

## Update process overview[​](#update-process-overview "Direct link to Update process overview")

1. The map update process is initiated by the API user or can be triggered manually.
2. The process downloads the newer data in the background while ensuring the full usability of the current (old) map dataset for browsing, search and navigation. The content is downloaded in a close-to-current user position order, i.e. nearby maps are downloaded first.
3. Once all new version data is downloaded:
   <!-- -->
   * If the update is applied via the `ContentUpdater.apply()` method, the update is atomically applied by replacing the files.

If the user's storage size does not allow the existence of the old and new dataset at the same time, the update requires handling:

4. The remaining offline maps which did not download because of space constraints can be downloaded by calling `ContentStoreItem.asyncDownload()` on each remaining item.

> 📝 **INFO**
>
> The auto-update behavior is different between the Navigation SDKs:
>
>* The C++ SDK does not provide an auto-update mechanism
>* The Kotlin SDK provides an update mechanism through ContentUpdater
>* The iOS SDK does not provide an auto-update mechanism

## Check for updates[​](#check-for-updates "Direct link to Check for updates")

You can listen for map updates by registering a callback with `OffboardListener` to monitor the status of road map versions.

* Kotlin
* Java

```kotlin
// Kotlin
val contentStore = ContentStore()
val errorCode = contentStore.checkForUpdate(EContentType.RoadMap)

if (errorCode == UnlError.NoError) {
    // Check initiated successfully
} else if (errorCode == UnlError.ConnectionRequired) {
    Log.e("Update", "No internet connection available")
}

```

```java
// Java
ContentStore contentStore = new ContentStore();
int errorCode = contentStore.checkForUpdate(EContentType.RoadMap);

if (errorCode == UnlError.NoError) {
    // Check initiated successfully
} else if (errorCode == UnlError.ConnectionRequired) {
    Log.e("Update", "No internet connection available");
}

```

The `checkForUpdate` method returns:

* `UnlError.NoError` if the check has been initiated
* `UnlError.ConnectionRequired` if no internet connection is available
* Other error codes for different failure scenarios

You can monitor status changes through the `OffboardListener` callbacks:

* Kotlin
* Java

```kotlin
// Kotlin
object : OffboardListener() {
    override fun onWorldwideRoadMapSupportStatus(state: EOffboardListenerStatus) {
        when (state) {
            EOffboardListenerStatus.UpToDate -> {
                Log.i("Update", "Map version is up-to-date")
            }
            EOffboardListenerStatus.OldData -> {
                Log.i("Update", "New map version available - online operations still supported")
            }
            EOffboardListenerStatus.ExpiredData -> {
                Log.w("Update", "Map version expired - only offline operations available")
            }
        }
    }
}

```

```java
// Java
new OffboardListener() {
    @Override
    public void onWorldwideRoadMapSupportStatus(EOffboardListenerStatus state) {
        if (state == EOffboardListenerStatus.UpToDate) {
            Log.i("Update", "Map version is up-to-date");
        } else if (state == EOffboardListenerStatus.OldData) {
            Log.i("Update", "New map version available - online operations still supported");
        } else if (state == EOffboardListenerStatus.ExpiredData) {
            Log.w("Update", "Map version expired - only offline operations available");
        }
    }
};

```

## Create content updater[​](#create-content-updater "Direct link to Create content updater")

To update road maps or styles, you must create a `ContentUpdater` object. This object manages all operations related to the update process:

* Kotlin
* Java

```kotlin
// Kotlin
val contentStore = ContentStore()
val (contentUpdater, creationError) = contentStore.createContentUpdater(EContentType.RoadMap)
    ?: return

if (creationError == UnlError.NoError) {
    // ContentUpdater successfully created
} else if (creationError == UnlError.Exist) {
    // A ContentUpdater for this type already exists, but it's still valid
} else {
    Log.e("Update", "Failed to create ContentUpdater: ${UnlError.getMessage(creationError)}")
    return
}

```

```java
// Java
ContentStore contentStore = new ContentStore();
Pair<ContentUpdater, Integer> result = contentStore.createContentUpdater(EContentType.RoadMap);
if (result == null) {
    return;
}

ContentUpdater contentUpdater = result.first;
int creationError = result.second;

if (creationError == UnlError.NoError) {
    // ContentUpdater successfully created
} else if (creationError == UnlError.Exist) {
    // A ContentUpdater for this type already exists, but it's still valid
} else {
    Log.e("Update", "Failed to create ContentUpdater: " + UnlError.getMessage(creationError));
    return;
}

```

The `createContentUpdater` method returns a `Pair<ContentUpdater?, Int>` with:

* A `ContentUpdater` instance (null if creation failed completely)

* An error code indicating the status:

  <!-- -->

  * `UnlError.NoError`: Successfully created and ready for use
  * `UnlError.Exist`: Already exists from a previous operation (still valid)
  * Other error codes: Creation failed

## Start the update[​](#start-the-update "Direct link to Start the update")

In order to start the update process, call the `update` method from the `ContentUpdater` object:

* Kotlin
* Java

```kotlin
// Kotlin
val (contentUpdater, creationError) = contentStore.createContentUpdater(EContentType.RoadMap) ?: return

val errorCode = contentUpdater?.update(
    allowChargeNetwork = true,  // Allow updates on charged networks
    listener = UnlProgressListener.create(
        onStarted = {
            Log.d("Update", "Update process started")
        },
        onProgress = { progress ->
            Log.d("Update", "Update progress: $progress/100")
        },
        onCompleted = { error, hint ->
            when (error) {
                UnlError.NoError -> {
                    Log.i("Update", "Update completed successfully")
                }
                UnlError.InUse -> {
                    Log.w("Update", "Update is already running")
                }
                UnlError.NoDiskSpace -> {
                    Log.e("Update", "Not enough disk space")
                }
                else -> {
                    Log.e("Update", "Update failed: ${UnlError.getMessage(error)}")
                }
            }
        },
        onStatusChanged = { status ->
            Log.d("Update", "Status changed to: $status")
        }
    )
)

```

```java
// Java
Pair<ContentUpdater, Integer> result = contentStore.createContentUpdater(EContentType.RoadMap);
if (result == null) return;

ContentUpdater contentUpdater = result.first;

int errorCode = contentUpdater.update(
    true,  // Allow updates on charged networks
    UnlProgressListener.create(
        () -> {
            Log.d("Update", "Update process started");
        },
        (progress) -> {
            Log.d("Update", "Update progress: " + progress + "/100");
        },
        (error, hint) -> {
            if (error == UnlError.NoError) {
                Log.i("Update", "Update completed successfully");
            } else if (error == UnlError.InUse) {
                Log.w("Update", "Update is already running");
            } else if (error == UnlError.NoDiskSpace) {
                Log.e("Update", "Not enough disk space");
            } else {
                Log.e("Update", "Update failed: " + UnlError.getMessage(error));
            }
        },
        (status) -> {
            Log.d("Update", "Status changed to: " + status);
        }
    )
);

```

The `update` method accepts:

* `allowChargeNetwork`: Boolean indicating whether the update can proceed on networks that may incur charges. If false, the update will only run on free networks like Wi-Fi.
* `listener`: A `UnlProgressListener` to track the operation progress and status

The method returns an `Int` error code:

* `UnlError.NoError` if the update process was successfully started
* `UnlError.InUse` if an update is already running
* `UnlError.NotSupported` if the update operation is not supported for the content type
* `UnlError.NoDiskSpace` if there is not enough space on the device
* Other error codes for other failure scenarios

## Content updater status[​](#content-updater-status "Direct link to Content updater status")

The `EContentUpdaterStatus` enum represents the current state of the update operation:

| Enum Value                 | Description                                                                                                                   |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `Idle`                     | The update process has not started. It's the default state of the `ContentUpdater`                                            |
| `WaitConnection`           | Waiting for an internet connection to proceed (Wi-Fi or mobile).                                                              |
| `WaitWIFIConnection`       | Waiting for a Wi-Fi connection before continuing. Used when the update was called with `allowChargeNetwork = false`.          |
| `CheckForUpdate`           | Checking for available updates.                                                                                               |
| `Download`                 | Downloading the updated content. The overall progress and the per-item progress is available.                                 |
| `FullyReady`               | The update is fully downloaded and ready to be applied. Call `apply()` to complete the update.                                |
| `PartiallyReady`           | The update is partially downloaded but can still be applied. Applying now will remove outdated items that weren't downloaded. |
| `DownloadRemainingContent` | Downloading any remaining content after applying a partial update.                                                            |
| `DownloadPendingContent`   | Downloads any pending content that has not yet been retrieved.                                                                |
| `Complete`                 | The update process has finished successfully.                                                                                 |
| `Error`                    | The update process encountered an error.                                                                                      |

You can check the current status and progress:

* Kotlin
* Java

```kotlin
// Kotlin
val contentUpdater: ContentUpdater = // ...
val status = contentUpdater.status
val progress = contentUpdater.progress
val canApply = contentUpdater.canApply()
val isStarted = contentUpdater.isStarted()

Log.d("Update", "Status: $status, Progress: $progress%, CanApply: $canApply")

```

```java
// Java
ContentUpdater contentUpdater = // ...
EContentUpdaterStatus status = contentUpdater.getStatus();
int progress = contentUpdater.getProgress();
boolean canApply = contentUpdater.canApply();
boolean isStarted = contentUpdater.isStarted();

Log.d("Update", "Status: " + status + ", Progress: " + progress + "%, CanApply: " + canApply);

```

## Apply the update[​](#apply-the-update "Direct link to Apply the update")

Once the update status reaches `FullyReady` or `PartiallyReady`, you can apply it using the `apply()` method:

* Kotlin
* Java

```kotlin
// Kotlin
val contentUpdater: ContentUpdater = // ...

if (contentUpdater.status == EContentUpdaterStatus.FullyReady ||
    contentUpdater.status == EContentUpdaterStatus.PartiallyReady) {

    if (!contentUpdater.canApply()) {
        Log.e("Update", "Cannot apply update at this time")
        return
    }

    val applyError = contentUpdater.apply()

    when (applyError) {
        UnlError.NoError -> {
            Log.i("Update", "Update applied successfully")
        }
        UnlError.UpToDate -> {
            Log.i("Update", "Already up to date")
        }
        UnlError.Invalidated -> {
            Log.e("Update", "Update was invalidated")
        }
        UnlError.Io -> {
            Log.e("Update", "File system error during apply")
        }
        else -> {
            Log.e("Update", "Failed to apply: ${UnlError.getMessage(applyError)}")
        }
    }
}

```

```java
// Java
ContentUpdater contentUpdater = // ...

if (contentUpdater.getStatus() == EContentUpdaterStatus.FullyReady ||
    contentUpdater.getStatus() == EContentUpdaterStatus.PartiallyReady) {

    if (!contentUpdater.canApply()) {
        Log.e("Update", "Cannot apply update at this time");
        return;
    }

    int applyError = contentUpdater.apply();

    if (applyError == UnlError.NoError) {
        Log.i("Update", "Update applied successfully");
    } else if (applyError == UnlError.UpToDate) {
        Log.i("Update", "Already up to date");
    } else if (applyError == UnlError.Invalidated) {
        Log.e("Update", "Update was invalidated");
    } else if (applyError == UnlError.Io) {
        Log.e("Update", "File system error during apply");
    } else {
        Log.e("Update", "Failed to apply: " + UnlError.getMessage(applyError));
    }
}

```

When the status is `PartiallyReady`, applying the update will:

* Remove outdated items that were not fully downloaded
* Restrict offline functionality to the updated content only
* Continue downloading the remaining items automatically

The `apply()` method returns an `Int` error code indicating the result of the operation.

## Resume a pending update[​](#resume-a-pending-update "Direct link to Resume a pending update")

The ContentUpdater supports operation resume between SDK running sessions. To check if there is a pending update:

* Kotlin
* Java

```kotlin
// Kotlin
val contentStore = ContentStore()
val (contentUpdater, creationError) = contentStore.createContentUpdater(EContentType.RoadMap) ?: return

contentUpdater?.let { updater ->
    // Check if there's a pending update from a previous session
    if (updater.status != EContentUpdaterStatus.Idle) {
        Log.i("Update", "Found pending update with status: ${updater.status}")

        // Resume the update
        updater.update(true, progressListener)
    }
}

```

```java
// Java
ContentStore contentStore = new ContentStore();
Pair<ContentUpdater, Integer> result = contentStore.createContentUpdater(EContentType.RoadMap);
if (result == null) return;

ContentUpdater contentUpdater = result.first;

if (contentUpdater != null) {
    // Check if there's a pending update from a previous session
    if (contentUpdater.getStatus() != EContentUpdaterStatus.Idle) {
        Log.i("Update", "Found pending update with status: " + contentUpdater.getStatus());

        // Resume the update
        contentUpdater.update(true, progressListener);
    }
}

```

## Cancel an update[​](#cancel-an-update "Direct link to Cancel an update")

You can cancel an ongoing update operation:

* Kotlin
* Java

```kotlin
// Kotlin
val contentUpdater: ContentUpdater = // ...
contentUpdater.cancel()

```

```java
// Java
ContentUpdater contentUpdater = // ...
contentUpdater.cancel();

```

Canceling will stop the update process and return the ContentUpdater to an idle state.
