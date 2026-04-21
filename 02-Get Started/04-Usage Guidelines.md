# Usage guidelines

## Legal requirements[​](#legal-requirements "Direct link to Legal requirements")

Some features, such as police and speed camera reports, may be illegal in certain countries. Ensure that the `safety` overlay and the corresponding entries in `socialReports` overlay are disabled where applicable. Additionally, restrict the availability of the `SocialOverlay` features based on current local regulations.

The UNL Android SDK utilizes data from OpenStreetMap. Please ensure proper attribution is given in accordance with the [OpenStreetMap license](https://osmfoundation.org/wiki/Licence).

Any use or display of Wikipedia content must include appropriate attribution, as outlined in the [Reusers' rights and obligations](https://en.wikipedia.org/wiki/Wikipedia:Copyrights#Reusers'_rights_and_obligations) section of Wikipedia's copyright guidelines.

## SDK thread[​](#sdk-thread "Direct link to SDK thread")

The Navigation SDK for Android is designed to be thread-safe. Most operations must be performed on specific threads to ensure optimal performance and avoid potential issues. You can interact with SDK objects only from the SDK thread. The SDK thread is initialized when the SDK is initialized and runs in the background. You can use the `SDKCall.execute` method to run code on the SDK thread.

* Kotlin
* Java

```kotlin
// Kotlin
SDKCall.execute {
    // Your code here
}

```

```java
// Java
SDKCall.execute(() -> {
    // Your code here
});

```

This ensures that the code inside the block runs on the SDK thread, allowing safe interaction with SDK objects.

> 🚨 **Danger**
>
> If you perform SDK operations from a different thread the sdk will throw an exception: `java.lang.Exception: Please ensure that your call is placed on UnlSdkThread or GLThread. See GemCall.execute{ ... }!`

## Units of measurement[​](#units-of-measurement "Direct link to Units of measurement")

The SDK primarily uses SI units for measurements, including:

* Meter (distance).
* Second (time).
* Kilogram (mass).
* Meters per second (speed).
* Watts for power (consumption).

The length value and measurement unit used in TTS (text-to-speech) instructions, such as "After 150 meters, bear left," are determined by the unit system configured through the `SDKSettings.unitSystem` setter.

> 🚨 **Danger**
>
> In certain cases, other units of measurement are used as they are better suited. For instance, the `height`, `length` and `width` fields of the `UnlTruckProfile` class are measured in centimeters. These exceptions are clearly indicated in the API reference.

## Error handling[​](#error-handling "Direct link to Error handling")

The Navigation SDK for Android uses a standardized error handling system through the `UnlError` class. All SDK operations return error codes that indicate the success or failure of the requested operation.

### Understanding Error Codes[​](#understanding-error-codes "Direct link to Understanding Error Codes")

Error codes are represented as integers (type alias `ErrorCode = Int`). The SDK provides various error constants in the `UnlError` object:

* **`UnlError.NoError` (0)**: Operation completed successfully
* **`UnlError.Cancel` (-3)**: Operation was canceled (not considered an error)
* **Negative values**: Indicate various error conditions

### Checking for Errors[​](#checking-for-errors "Direct link to Checking for Errors")

Use `UnlError.isError()` to determine if an error code represents an actual error:

* Kotlin
* Java

```kotlin
// Kotlin
val errorCode = someSDKOperation()
if (UnlError.isError(errorCode)) {
    // Handle the error
    val errorMessage = UnlError.getMessage(errorCode, context)
    Log.e("SDK", "Operation failed: $errorMessage")
} else {
    // Operation succeeded or was canceled
}

```

```java
// Java
int errorCode = someSDKOperation();
if (UnlError.isError(errorCode)) {
    // Handle the error
    String errorMessage = UnlError.getMessage(errorCode, context);
    Log.e("SDK", "Operation failed: " + errorMessage);
} else {
    // Operation succeeded or was canceled
}

```

### Getting Error Messages[​](#getting-error-messages "Direct link to Getting Error Messages")

The `UnlError.getMessage()` method provides human-readable error descriptions:

* Kotlin
* Java

```kotlin
// Kotlin
val errorMessage = UnlError.getMessage(errorCode, context)

```

```java
// Java
String errorMessage = UnlError.getMessage(errorCode, context);

```

### Common Error Types[​](#common-error-types "Direct link to Common Error Types")

The SDK defines several categories of errors:

**File and I/O Errors:**

* `UnlError.Io` (-6): General I/O error
* `UnlError.AccessDenied` (-7): Access denied to resource
* `UnlError.NotFound` (-11): Required item not found
* `UnlError.NoDiskSpace` (-9): Insufficient disk space

**Network Errors:**

* `UnlError.NoConnection` (-24): No network connection available
* `UnlError.NetworkTimeout` (-29): Network operation timed out
* `UnlError.NetworkFailed` (-23): Network connection failed
* `UnlError.ConnectionRequired` (-25): Network connection required

**Routing and Navigation Errors:**

* `UnlError.NoRoute` (-18): No route could be calculated
* `UnlError.WaypointAccess` (-19): One or more waypoints not accessible
* `UnlError.RouteTooLong` (-20): Route is too long, use intermediate waypoints

**Resource and Memory Errors:**

* `UnlError.NoMemory` (-14): Insufficient memory
* `UnlError.ResourceMissing` (-36): Required resources missing, reinstall needed
* `UnlError.InvalidInput` (-15): Invalid input provided

### Handling Asynchronous Operations[​](#handling-asynchronous-operations "Direct link to Handling Asynchronous Operations")

Many SDK operations are asynchronous and use listeners to report completion. Use `UnlProgressListener` to handle errors in async operations:

* Kotlin
* Java

```kotlin
// Kotlin
val listener = UnlProgressListener.create(
    onCompleted = { errorCode, hint ->
        if (UnlError.isError(errorCode)) {
            val errorMessage = UnlError.getMessage(errorCode, this)
            // Handle error appropriately
            showErrorDialog("Operation failed: $errorMessage")
        } else {
            // Handle successful completion
            onOperationSuccess()
        }
    }
)

```

```java
// Java
UnlProgressListener listener = UnlProgressListener.create(
    (errorCode, hint) -> {
        if (UnlError.isError(errorCode)) {
            String errorMessage = UnlError.getMessage(errorCode, this);
            // Handle error appropriately
            showErrorDialog("Operation failed: " + errorMessage);
        } else {
            // Handle successful completion
            onOperationSuccess();
        }
    }
);

```

### Best Practices[​](#best-practices "Direct link to Best Practices")

1. **Always check error codes**: Never assume operations succeed without checking the return value
2. **Provide user feedback**: Convert technical error codes to user-friendly messages
3. **Handle network errors gracefully**: Implement retry logic for network-related operations
4. **Log errors for debugging**: Include error codes and messages in your application logs
5. **Handle specific error types**: Implement different handling strategies for different error categories

* Kotlin
* Java

```kotlin
// Kotlin
// Example: Comprehensive error handling
fun handleSDKError(errorCode: ErrorCode, context: Context) {
    when (errorCode) {
        UnlError.NoError -> {
            // Success - no action needed
        }
        UnlError.Cancel -> {
            // Operation was canceled by user
            showMessage("Operation canceled")
        }
        UnlError.NoConnection,
        UnlError.NetworkFailed,
        UnlError.NetworkTimeout -> {
            // Network-related errors
            showRetryDialog("Network error. Please check your connection and try again.")
        }
        UnlError.NoRoute -> {
            // Routing specific error
            showMessage("Could not calculate route. Please check your destination.")
        }
        UnlError.ResourceMissing -> {
            // Critical error requiring app reinstall
            showMessage("App resources are missing. Please reinstall the application.")
        }
        else -> {
            // Generic error handling
            val message = UnlError.getMessage(errorCode, context)
            showMessage("Error: $message")
        }
    }
}

```

```java
// Java
// Example: Comprehensive error handling
public void handleSDKError(int errorCode, Context context) {
    if (errorCode == UnlError.NoError) {
        // Success - no action needed
    } else if (errorCode == UnlError.Cancel) {
        // Operation was canceled by user
        showMessage("Operation canceled");
    } else if (errorCode == UnlError.NoConnection
            || errorCode == UnlError.NetworkFailed
            || errorCode == UnlError.NetworkTimeout) {
        // Network-related errors
        showRetryDialog("Network error. Please check your connection and try again.");
    } else if (errorCode == UnlError.NoRoute) {
        // Routing specific error
        showMessage("Could not calculate route. Please check your destination.");
    } else if (errorCode == UnlError.ResourceMissing) {
        // Critical error requiring app reinstall
        showMessage("App resources are missing. Please reinstall the application.");
    } else {
        // Generic error handling
        String message = UnlError.getMessage(errorCode, context);
        showMessage("Error: " + message);
    }
}

```

## Do not extend the classes provided by the SDK[​](#do-not-extend-the-classes-provided-by-the-sdk "Direct link to Do not extend the classes provided by the SDK")

The SDK is designed to deliver all necessary functionalities in an intuitive and straightforward manner. Avoid extending any of the Navigation SDK for Android classes. Use the callback methods provided by the SDK for seamless integration.

## DateTime Parameter Rules[​](#datetime-parameter-rules "Direct link to DateTime Parameter Rules")

Make sure the `DateTime` passed to method is a valid positive utc value. Values below the `DateTime.utc(0)` are not supported.

## General principles related to listeners[​](#general-principles-related-to-listeners "Direct link to General principles related to listeners")

* Use the provided register methods or constructor parameters **to subscribe** to events.
* Only one callback can be active per event type. If multiple callbacks are registered for the same event type, the most recently registered callback will **override** the previous one.
* **To unsubscribe** from events, register an empty callback.

