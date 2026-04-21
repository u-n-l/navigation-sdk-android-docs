# Timezone service

The `UnlTimezoneService` provides functionality for managing and retrieving time zone information. It allows you to transform a UTC `UnlTime` to a specific time zone, but also provides other details regarding the offset and the timezone.

The `UnlTimezoneService` object provides methods to get timezone information online (more accurate and takes into account new changes) or offline (faster, works regardless of network access but may be outdated).

* `getTimezoneInfoWithCoordinates` and `getTimezoneInfoWithTimezoneId` methods retrieve timezone information asynchronously.
* Both methods support an `accurateResult` parameter that determines whether to use online services (more accurate) or offline data (faster).

## The UnlTimezoneResult structure[​](#the-timezoneresult-structure "Direct link to The UnlTimezoneResult structure")

The `UnlTimezoneResult` class represents the result of a time zone lookup operation. It contains the following properties:

| Member       | Type        | Description                                           |
| ------------ | ----------- | ------------------------------------------------------------------------------------------------------ |
| `dstOffset`  | `Int`       | Daylight Saving UnlTime (DST) offset in seconds.                                                          |
| `utcOffset`  | `Int`       | The raw UTC offset in seconds, excluding DST. Can be negative.                                         |
| `offset`     | `Int`       | The total offset in seconds in respect to UTC (`dstOffset` + `utcOffset`). Can be negative.            |
| `status`     | `ETZStatus` | Status of the response. See the values below.                                                          |
| `timezoneId` | `String?`   | The timezone identifier in format `Continent/City_Name`. Examples: `Europe/Paris`, `America/New_York`. |
| `localTime`  | `UnlTime?`     | The local time as a `UnlTime` object representing the local time of the requested timezone.               |

> 🚨 **DANGER**
>
> The `UnlTimezoneResult` properties are read-only and can only be accessed after a successful timezone lookup operation.

The `ETZStatus` enum represents the status of a time zone lookup operation. It can have the following values:

* `Success` - the request was successful.
* `InvalidCoordinate` - the provided geographic coordinates were invalid or out of range.
* `WrongTimezoneId` - the provided timezone identifier was malformed or not recognized.
* `WrongTimestamp` - the provided timestamp (UnlTime) was invalid or could not be parsed.
* `TimezoneNotFound` - no timezone could be found for the given input.

## Get timezone info by coordinates[​](#get-timezone-info-by-coordinates "Direct link to Get timezone info by coordinates")

The `getTimezoneInfoWithCoordinates` method allows you to retrieve time zone information based on geographic coordinates (latitude and longitude) and a UTC `UnlTime` object. This can be useful for applications that need to determine the local time zone for a specific location.

### Basic usage example[​](#basic-usage-example "Direct link to Basic usage example")

* Kotlin
* Java

```kotlin
// Kotlin
    val timezoneResult = UnlTimezoneResult()
    val coordinates = UnlCoordinates(55.626, 37.457)
    val utcTime = UnlTime.getUniversalTime() // Current UTC time

    val timezoneListener = UnlProgressListener.create(
        onCompleted = { errorCode, _ ->
            if (errorCode == UnlError.NoError) {
                // Success - access timezone data
                when (timezoneResult.status) {
                    ETZStatus.Success -> {
                        val timezoneId = timezoneResult.timezoneId
                        val offset = timezoneResult.offset // Total offset in seconds
                        val dstOffset = timezoneResult.dstOffset // DST offset in seconds
                        val utcOffset = timezoneResult.utcOffset // UTC offset in seconds
                        val localTime = timezoneResult.localTime

                        // Use the timezone information
                        //displayTimezoneInfo(timezoneId, offset, localTime)
                    }

                    ETZStatus.InvalidCoordinate -> {
                        Log.e("UnlTimezoneService", "Invalid coordinates provided")
                    }

                    ETZStatus.TimezoneNotFound -> {
                        Log.e("UnlTimezoneService", "No timezone found for location")
                    }

                    else -> {
                        Log.e("UnlTimezoneService", "Timezone lookup failed: ${timezoneResult.status}")
                    }
                }
            } else {
                Log.e("UnlTimezoneService", "Request failed: ${UnlError.getMessage(errorCode)}")
            }
        },

        postOnMain = true
    )

    // Request timezone information
    // accurateResult = true: Use online service (more accurate, requires internet)
    // accurateResult = false: Use offline data (faster, may be outdated)
    val errorCode = UnlTimezoneService.getTimezoneInfoWithCoordinates(
        timezoneResult = timezoneResult,
        coordinates = coordinates,
        time = utcTime!!,
        listener = timezoneListener,
        accurateResult = true
    )

    if (UnlError.isError(errorCode)) {
        Log.e("UnlTimezoneService", "Failed to start timezone request: ${UnlError.getMessage(errorCode)}")
    }

```

```java
// Java
    UnlTimezoneResult timezoneResult = new UnlTimezoneResult();
    UnlCoordinates coordinates = new UnlCoordinates(55.626, 37.457);
    UnlTime utcTime = UnlTime.getUniversalTime(); // Current UTC time

    UnlProgressListener timezoneListener = UnlProgressListener.create(
        (errorCode, hint) -> {
            if (errorCode == UnlError.NoError) {
                // Success - access timezone data
                if (timezoneResult.getStatus() == ETZStatus.Success) {
                    String timezoneId = timezoneResult.getTimezoneId();
                    int offset = timezoneResult.getOffset(); // Total offset in seconds
                    int dstOffset = timezoneResult.getDstOffset(); // DST offset in seconds
                    int utcOffset = timezoneResult.getUtcOffset(); // UTC offset in seconds
                    UnlTime localTime = timezoneResult.getLocalTime();

                    // Use the timezone information
                    //displayTimezoneInfo(timezoneId, offset, localTime);
                } else if (timezoneResult.getStatus() == ETZStatus.InvalidCoordinate) {
                    Log.e("UnlTimezoneService", "Invalid coordinates provided");
                } else if (timezoneResult.getStatus() == ETZStatus.TimezoneNotFound) {
                    Log.e("UnlTimezoneService", "No timezone found for location");
                } else {
                    Log.e("UnlTimezoneService", "Timezone lookup failed: " + timezoneResult.getStatus());
                }
            } else {
                Log.e("UnlTimezoneService", "Request failed: " + UnlError.getMessage(errorCode));
            }
        },
        true
    );

    // Request timezone information
    // accurateResult = true: Use online service (more accurate, requires internet)
    // accurateResult = false: Use offline data (faster, may be outdated)
    int errorCode = UnlTimezoneService.getTimezoneInfoWithCoordinates(
        timezoneResult,
        coordinates,
        utcTime,
        timezoneListener,
        true
    );

    if (UnlError.isError(errorCode)) {
        Log.e("UnlTimezoneService", "Failed to start timezone request: " + UnlError.getMessage(errorCode));
    }

```

### Using offline data[​](#using-offline-data "Direct link to Using offline data")

For faster results that don't require internet connectivity, use `accurateResult = false`:

* Kotlin
* Java

```kotlin
// Kotlin
    val timezoneResult = UnlTimezoneResult()
    val coordinates = UnlCoordinates(55.626, 37.457)
    val utcTime = UnlTime.getUniversalTime()

    val timezoneListener = UnlProgressListener.create(
        onCompleted = { errorCode, _ ->
            if (errorCode == UnlError.NoError && timezoneResult.status == ETZStatus.Success) {
                // Process offline timezone result
                val localTime = timezoneResult.localTime
                val timezoneId = timezoneResult.timezoneId
                // Note: Offline data may be outdated
            }
        },
        postOnMain = true
    )

    // Use offline data for faster response
    UnlTimezoneService.getTimezoneInfoWithCoordinates(
        timezoneResult,
        coordinates,
        utcTime!!,
        timezoneListener,
        accurateResult = false
    )

```

```java
// Java
    UnlTimezoneResult timezoneResult = new UnlTimezoneResult();
    UnlCoordinates coordinates = new UnlCoordinates(55.626, 37.457);
    UnlTime utcTime = UnlTime.getUniversalTime();

    UnlProgressListener timezoneListener = UnlProgressListener.create(
        (errorCode, hint) -> {
            if (errorCode == UnlError.NoError && timezoneResult.getStatus() == ETZStatus.Success) {
                // Process offline timezone result
                UnlTime localTime = timezoneResult.getLocalTime();
                String timezoneId = timezoneResult.getTimezoneId();
                // Note: Offline data may be outdated
            }
        },
        true
    );

    // Use offline data for faster response
    UnlTimezoneService.getTimezoneInfoWithCoordinates(
        timezoneResult,
        coordinates,
        utcTime,
        timezoneListener,
        false
    );

```

## Get timezone info by timezone ID[​](#get-timezone-info-by-timezone-id "Direct link to Get timezone info by timezone ID")

The `getTimezoneInfoWithTimezoneId` method allows you to retrieve time zone information based on a specific timezone ID and a UTC `UnlTime` object.

### Basic usage example[​](#basic-usage-example-1 "Direct link to Basic usage example")

* Kotlin
* Java

```kotlin
// Kotlin
    val timezoneResult = UnlTimezoneResult()
    val timezoneId = "Europe/Moscow"
    val utcTime = UnlTime.getUniversalTime()

    val timezoneListener = UnlProgressListener.create(
        onCompleted = { errorCode, _ ->
            if (errorCode == UnlError.NoError) {
                when (timezoneResult.status) {
                    ETZStatus.Success -> {
                        val offset = timezoneResult.offset
                        val localTime = timezoneResult.localTime
                        val dstOffset = timezoneResult.dstOffset

                        // Use the timezone information
                        //processTimezoneData(offset, localTime, dstOffset)
                    }

                    ETZStatus.WrongTimezoneId -> {
                        Log.e("UnlTimezoneService", "Invalid timezone ID: $timezoneId")
                    }

                    else -> {
                        Log.e("UnlTimezoneService", "Timezone lookup failed")
                    }
                }
            } else {
                Log.e("UnlTimezoneService", "Request failed: ${UnlError.getMessage(errorCode)}")
            }
        },

        postOnMain = true
    )

    // Request timezone information by ID
    val errorCode = UnlTimezoneService.getTimezoneInfoWithTimezoneId(
        timezoneResult = timezoneResult,
        timezoneId = timezoneId,
        time = utcTime!!,
        listener = timezoneListener,
        accurateResult = true
    )

    if (UnlError.isError(errorCode)) {
        Log.e("UnlTimezoneService", "Failed to start timezone request")
    }

```

```java
// Java
    UnlTimezoneResult timezoneResult = new UnlTimezoneResult();
    String timezoneId = "Europe/Moscow";
    UnlTime utcTime = UnlTime.getUniversalTime();

    UnlProgressListener timezoneListener = UnlProgressListener.create(
        (errorCode, hint) -> {
            if (errorCode == UnlError.NoError) {
                if (timezoneResult.getStatus() == ETZStatus.Success) {
                    int offset = timezoneResult.getOffset();
                    UnlTime localTime = timezoneResult.getLocalTime();
                    int dstOffset = timezoneResult.getDstOffset();

                    // Use the timezone information
                    //processTimezoneData(offset, localTime, dstOffset);
                } else if (timezoneResult.getStatus() == ETZStatus.WrongTimezoneId) {
                    Log.e("UnlTimezoneService", "Invalid timezone ID: " + timezoneId);
                } else {
                    Log.e("UnlTimezoneService", "Timezone lookup failed");
                }
            } else {
                Log.e("UnlTimezoneService", "Request failed: " + UnlError.getMessage(errorCode));
            }
        },
        true
    );

    // Request timezone information by ID
    int errorCode = UnlTimezoneService.getTimezoneInfoWithTimezoneId(
        timezoneResult,
        timezoneId,
        utcTime,
        timezoneListener,
        true
    );

    if (UnlError.isError(errorCode)) {
        Log.e("UnlTimezoneService", "Failed to start timezone request");
    }

```

### Using offline data[​](#using-offline-data-1 "Direct link to Using offline data")

For faster results without internet dependency:

* Kotlin
* Java

```kotlin
// Kotlin
    val timezoneResult = UnlTimezoneResult()

    val timezoneListener = UnlProgressListener.create(
        onCompleted = { errorCode, _ ->
            if (errorCode == UnlError.NoError && timezoneResult.status == ETZStatus.Success) {
                // Process offline result
                val offset = timezoneResult.offset
                val localTime = timezoneResult.localTime
            }
        },
        postOnMain = true
    )

    UnlTimezoneService.getTimezoneInfoWithTimezoneId(
        timezoneResult,
        "Europe/Moscow",
        UnlTime.getUniversalTime()!!,
        timezoneListener,
        accurateResult = false // Use offline data
    )

```

```java
// Java
    UnlTimezoneResult timezoneResult = new UnlTimezoneResult();

    UnlProgressListener timezoneListener = UnlProgressListener.create(
        (errorCode, hint) -> {
            if (errorCode == UnlError.NoError && timezoneResult.getStatus() == ETZStatus.Success) {
                // Process offline result
                int offset = timezoneResult.getOffset();
                UnlTime localTime = timezoneResult.getLocalTime();
            }
        },
        true
    );

    UnlTimezoneService.getTimezoneInfoWithTimezoneId(
        timezoneResult,
        "Europe/Moscow",
        UnlTime.getUniversalTime(),
        timezoneListener,
        false // Use offline data
    );

```

## Working with UnlTime objects[​](#working-with-time-objects "Direct link to Working with UnlTime objects")

The `UnlTime` class provides utilities for creating and manipulating time values:

* Kotlin
* Java

```kotlin
// Kotlin
// Create UTC time
val utcTime = UnlTime.getUniversalTime()

// Create local time
val localTime = UnlTime.getLocalTime()

// Create custom time
val customTime = UnlTime().apply {
    year = 2025
    month = 7
    day = 1
    hour = 6
    minute = 0
    second = 0
}

// Convert to timestamp (milliseconds since epoch)
val timestamp = customTime.asLong()

// Create from timestamp
val timeFromTimestamp = UnlTime().apply {
    fromLong(timestamp)
}

```

```java
// Java
// Create UTC time
UnlTime utcTime = UnlTime.getUniversalTime();

// Create local time
UnlTime localTime = UnlTime.getLocalTime();

// Create custom time
UnlTime customTime = new UnlTime();
customTime.setYear(2025);
customTime.setMonth(7);
customTime.setDay(1);
customTime.setHour(6);
customTime.setMinute(0);
customTime.setSecond(0);

// Convert to timestamp (milliseconds since epoch)
long timestamp = customTime.asLong();

// Create from timestamp
UnlTime timeFromTimestamp = new UnlTime();
timeFromTimestamp.fromLong(timestamp);

```
