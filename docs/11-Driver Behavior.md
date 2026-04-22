# Driver Behaviour

The Driver Behaviour feature enables the analysis and scoring of a driver's behavior during a trip, identifying risky driving patterns and summarizing them with safety scores. This feature tracks both real-time and session-level driving events, such as harsh braking, cornering, or ignoring traffic signs, and evaluates overall risk using multiple criteria.

This data can be used to offer user feedback, identify unsafe habits, and assess safety levels over time. All information is processed using on-device sensor data (via the configured `DataSource`) and optionally matched to the road network if `useMapMatchedPosition` is enabled. 

## Starting and Stopping Analysis[​](#starting-and-stopping-analysis "Direct link to Starting and Stopping Analysis")

To use the Driver Behaviour module, you first need to create a data source and then produce a `DriverBehaviour` instance using the factory method. The session is started using `startAnalysis()` and closed using `stopAnalysis()`, which returns a `DriverBehaviourAnalysis` instance representing the complete analysis.

* Kotlin
* Java

```kotlin
// Kotlin
val dataSource = DataSourceFactory.produceLive()
val driverBehaviour = DriverBehaviour.produce(dataSource!!, useMapMatchedPosition = true)

val started = driverBehaviour?.startAnalysis() ?: false

if (!started) {
    Log.e("DriverBehaviour", "Failed to start analysis")
    return
}

// ... after some driving

val result = driverBehaviour.stopAnalysis()

if (result?.valid != true) {
    Log.w("DriverBehaviour", "The analysis is invalid and cannot be used")
    return
}

// Process the analysis result
// processAnalysisResult(result)

```

```java
// Java
DataSource dataSource = DataSourceFactory.produceLive();
DriverBehaviour driverBehaviour = DriverBehaviour.produce(dataSource, true);

boolean started = false;
if (driverBehaviour != null) {
    started = driverBehaviour.startAnalysis();
}

if (!started) {
    Log.e("DriverBehaviour", "Failed to start analysis");
    return;
}

// ... after some driving

DriverBehaviourAnalysis result = driverBehaviour.stopAnalysis();

if (result == null || !result.getValid()) {
    Log.w("DriverBehaviour", "The analysis is invalid and cannot be used");
    return;
}

// Process the analysis result
// processAnalysisResult(result);

```

> 🚨 **DANGER**
>
> All `DriverBehaviourAnalysis` instances expose a `valid` property to determine whether the analysis is valid. Always verify this property before accessing or relying on the data it contains.

## Inspecting a Driving Session[​](#inspecting-a-driving-session "Direct link to Inspecting a Driving Session")

The result returned by `stopAnalysis()` (or via `lastAnalysis`) contains aggregate and detailed information on the trip:

* Kotlin
* Java

```kotlin
// Kotlin
if (result?.valid != true) {
    Log.w("DriverBehaviour", "The analysis is invalid and cannot be used")
    return
}

val startTime = result.startTime // UnlTime object
val finishTime = result.finishTime // UnlTime object
val distance = result.kilometersDriven // Double
val drivingDuration = result.minutesDriven // Double
val speedingTime = result.minutesSpeeding // Double
val totalElapsedTime = result.minutesTotalElapsed // Double
val tailgatingTime = result.minutesTailgating // Double

```

```java
// Java
if (result == null || !result.getValid()) {
    Log.w("DriverBehaviour", "The analysis is invalid and cannot be used");
    return;
}

UnlTime startTime = result.getStartTime(); // UnlTime object
UnlTime finishTime = result.getFinishTime(); // UnlTime object
double distance = result.getKilometersDriven(); // Double
double drivingDuration = result.getMinutesDriven(); // Double
double speedingTime = result.getMinutesSpeeding(); // Double
double totalElapsedTime = result.getMinutesTotalElapsed(); // Double
double tailgatingTime = result.getMinutesTailgating(); // Double

```

The session also includes risk scores:

* Kotlin
* Java

```kotlin
// Kotlin
val scores = result.drivingScores
if (scores == null) {
    Log.w("DriverBehaviour", "No driving scores available")
    return
}

val speedRisk = scores.speedAverageRiskScore
val speedVariableRisk = scores.speedVariableRiskScore
val harshAccelerationRisk = scores.harshAccelerationScore
val brakingRisk = scores.harshBrakingScore
val swervingRisk = scores.swervingScore
val corneringRisk = scores.corneringScore
val tailgatingRisk = scores.tailgatingScore
val ignoredSignsRisk = scores.ignoredStopSignsScore
val fatigue = scores.fatigueScore
val overallScore = scores.aggregateScore

```

```java
// Java
DrivingScores scores = result.getDrivingScores();
if (scores == null) {
    Log.w("DriverBehaviour", "No driving scores available");
    return;
}

double speedRisk = scores.getSpeedAverageRiskScore();
double speedVariableRisk = scores.getSpeedVariableRiskScore();
double harshAccelerationRisk = scores.getHarshAccelerationScore();
double brakingRisk = scores.getHarshBrakingScore();
double swervingRisk = scores.getSwervingScore();
double corneringRisk = scores.getCorneringScore();
double tailgatingRisk = scores.getTailgatingScore();
double ignoredSignsRisk = scores.getIgnoredStopSignsScore();
double fatigue = scores.getFatigueScore();
double overallScore = scores.getAggregateScore();

```

> 📝 **INFO**
>
> Each score ranges from 0 (unsafe) to 100 (safe). A score of -1 means invalid or unavailable.

## Inspecting Driving Events[​](#inspecting-driving-events "Direct link to Inspecting Driving Events")

Use the `drivingEvents` property of the session result to access discrete driving incidents that were detected:

* Kotlin
* Java

```kotlin
// Kotlin
val events = result.drivingEvents
events?.asArrayList()?.forEach { event ->
    val time = event.time
    val latitude = event.latitudeDeg
    val longitude = event.longitudeDeg
    val eventType = event.eventType

    Log.d("DriverBehaviour",
        "Event at $latitude, $longitude at ${time?.asLong()} with type $eventType")
}

```

```java
// Java
DrivingEventList events = result.getDrivingEvents();
if (events != null) {
    for (DrivingEvent event : events.asArrayList()) {
        UnlTime time = event.getTime();
        double latitude = event.getLatitudeDeg();
        double longitude = event.getLongitudeDeg();
        EDrivingEvent eventType = event.getEventType();

        Log.d("DriverBehaviour",
            "Event at " + latitude + ", " + longitude + " at " + (time != null ? time.asLong() : 0) + " with type " + eventType);
    }
}

```

Event types are defined by the `EDrivingEvent` enum:

## Driving Event Types[​](#driving-event-types "Direct link to Driving Event Types")

| Enum Value          | Description            |
| ------------------- | ---------------------- |
| `NoEvent`           | No event               |
| `StartingTrip`      | Starting a trip        |
| `FinishingTrip`     | Finishing a trip       |
| `Resting`           | Resting                |
| `HarshAcceleration` | Harsh acceleration     |
| `HarshBraking`      | Harsh braking          |
| `Cornering`         | Cornering              |
| `Swerving`          | Swerving               |
| `Tailgating`        | Tailgating             |
| `IgnoringSigns`     | Ignoring traffic signs |

## Real-time Feedback[​](#real-time-feedback "Direct link to Real-time Feedback")

If the analysis is ongoing, you can fetch real-time scores using:

* Kotlin
* Java

```kotlin
// Kotlin
val instantScores = driverBehaviour?.instantaneousScores
instantScores?.let { scores ->
    val currentSpeedRisk = scores.speedAverageRiskScore
    val currentBrakingRisk = scores.harshBrakingScore
    val currentOverallScore = scores.aggregateScore

    // Use scores for real-time feedback
    updateUIWithScores(currentSpeedRisk, currentBrakingRisk, currentOverallScore)
}

```

```java
// Java
DrivingScores instantScores = driverBehaviour != null ? driverBehaviour.getInstantaneousScores() : null;
if (instantScores != null) {
    double currentSpeedRisk = instantScores.getSpeedAverageRiskScore();
    double currentBrakingRisk = instantScores.getHarshBrakingScore();
    double currentOverallScore = instantScores.getAggregateScore();

    // Use scores for real-time feedback
    updateUIWithScores(currentSpeedRisk, currentBrakingRisk, currentOverallScore);
}

```

These reflect the user's current behavior and are useful for immediate in-app feedback.

## Stop Analysis and Get Last Analysis[​](#stop-analysis-and-get-last-analysis "Direct link to Stop Analysis and Get Last Analysis")

To stop an ongoing analysis you can use:

* Kotlin
* Java

```kotlin
// Kotlin
val analysis = driverBehaviour?.stopAnalysis()
if (analysis?.valid != true) {
    Log.w("DriverBehaviour", "No valid analysis available")
    return
}

// Process the completed analysis
processCompletedAnalysis(analysis)

```

```java
// Java
DriverBehaviourAnalysis analysis = driverBehaviour != null ? driverBehaviour.stopAnalysis() : null;
if (analysis == null || !analysis.getValid()) {
    Log.w("DriverBehaviour", "No valid analysis available");
    return;
}

// Process the completed analysis
processCompletedAnalysis(analysis);

```

You can also retrieve the last completed analysis:

* Kotlin
* Java

```kotlin
// Kotlin
val lastAnalysis = driverBehaviour?.lastAnalysis
if (lastAnalysis?.valid != true) {
    Log.w("DriverBehaviour", "No valid analysis available")
    return
}

// Process the last analysis
processLastAnalysis(lastAnalysis)

```

```java
// Java
DriverBehaviourAnalysis lastAnalysis = driverBehaviour != null ? driverBehaviour.getLastAnalysis() : null;
if (lastAnalysis == null || !lastAnalysis.getValid()) {
    Log.w("DriverBehaviour", "No valid analysis available");
    return;
}

// Process the last analysis
processLastAnalysis(lastAnalysis);

```

## Retrieve Past Analyses[​](#retrieve-past-analyses "Direct link to Retrieve Past Analyses")

All completed sessions are stored locally and accessible via:

* Kotlin
* Java

```kotlin
// Kotlin
val pastSessions = driverBehaviour?.allDriverBehaviourAnalyses
pastSessions?.forEach { analysis ->
    if (analysis.valid) {
        // Process each valid analysis
        processAnalysis(analysis)
    }
}

```

```java
// Java
List<DriverBehaviourAnalysis> pastSessions = driverBehaviour != null ? driverBehaviour.getAllDriverBehaviourAnalyses() : null;
if (pastSessions != null) {
    for (DriverBehaviourAnalysis analysis : pastSessions) {
        if (analysis.getValid()) {
            // Process each valid analysis
            processAnalysis(analysis);
        }
    }
}

```

You can also obtain a combined analysis over a time interval:

* Kotlin
* Java

```kotlin
// Kotlin
val startTime = UnlTime().apply {
    longValue = System.currentTimeMillis() - (7 * 24 * 60 * 60 * 1000) // 7 days ago
}
val endTime = UnlTime().apply {
    longValue = System.currentTimeMillis() // Now
}

val combined = driverBehaviour?.getCombinedAnalysis(startTime, endTime)
if (combined?.valid == true) {
    // Process combined analysis
    processCombinedAnalysis(combined)
}

```

```java
// Java
UnlTime startTime = new UnlTime();
startTime.setLongValue(System.currentTimeMillis() - (7L * 24 * 60 * 60 * 1000)); // 7 days ago

UnlTime endTime = new UnlTime();
endTime.setLongValue(System.currentTimeMillis()); // Now

DriverBehaviourAnalysis combined = driverBehaviour != null ? driverBehaviour.getCombinedAnalysis(startTime, endTime) : null;
if (combined != null && combined.getValid()) {
    // Process combined analysis
    processCombinedAnalysis(combined);
}

```

## Analyses Storage Location[​](#analyses-storage-location "Direct link to Analyses Storage Location")

Driver behaviour analyses are stored locally on the device. Inside the app's directory, a folder named **`DriverBehaviour`** is created (at the same level as `Data`).

## Data Cleanup[​](#data-cleanup "Direct link to Data Cleanup")

To save space or comply with privacy policies, older sessions can be erased:

* Kotlin
* Java

```kotlin
// Kotlin
val thirtyDaysAgo = UnlTime().apply {
    longValue = System.currentTimeMillis() - (30L * 24 * 60 * 60 * 1000) // 30 days ago
}

driverBehaviour?.eraseAnalysesOlderThan(thirtyDaysAgo)

```

```java
// Java
UnlTime thirtyDaysAgo = new UnlTime();
thirtyDaysAgo.setLongValue(System.currentTimeMillis() - (30L * 24 * 60 * 60 * 1000)); // 30 days ago

if (driverBehaviour != null) {
    driverBehaviour.eraseAnalysesOlderThan(thirtyDaysAgo);
}

```

> 📝 **INFO**
>
> Driver behaviour analysis requires a properly configured DataSource. Use `DataSourceFactory.produceLive()` to create a live data source for real-time position updates. To ensure reliable results, make sure to start and stop the analysis appropriately and avoid frequent interruptions or overlapping sessions.

## Memory Management[​](#memory-management "Direct link to Memory Management")

Remember to properly release resources when done:

* Kotlin
* Java

```kotlin
// Kotlin
driverBehaviour?.stopAnalysis()
driverBehaviour?.release()
dataSource?.release()

```

```java
// Java
if (driverBehaviour != null) {
    driverBehaviour.stopAnalysis();
    driverBehaviour.release();
}
if (dataSource != null) {
    dataSource.release();
}

```

## Background Location[​](#background-location "Direct link to Background Location")

For driver behavior analysis while the app is in the background, ensure your app has the necessary location permissions:

* Kotlin
* Java

```kotlin
// Kotliln
private fun requestPermissions(activity: Activity): Boolean {
    val permissions = arrayOf(
        Manifest.permission.ACCESS_FINE_LOCATION,
        Manifest.permission.ACCESS_COARSE_LOCATION,
        Manifest.permission.ACCESS_BACKGROUND_LOCATION // For API 29+
    )

    return PermissionsHelper.requestPermissions(REQUEST_PERMISSIONS, activity, permissions)
}

```

```java
// Java
private boolean requestPermissions(Activity activity) {
    String[] permissions = new String[]{
        Manifest.permission.ACCESS_FINE_LOCATION,
        Manifest.permission.ACCESS_COARSE_LOCATION,
        Manifest.permission.ACCESS_BACKGROUND_LOCATION // For API 29+
    };

    return PermissionsHelper.requestPermissions(REQUEST_PERMISSIONS, activity, permissions);
}

```
