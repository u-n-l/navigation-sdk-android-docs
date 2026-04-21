# Speed warnings

The SDK provides features for monitoring and notifying users about speed limits and violations. You can configure alerts for when a user exceeds the speed limit, when the speed limit changes on the new road segment with respect to the previous, and when the user returns to a normal speed range (onNormalSpeed). The SDK also allows you to set customizable thresholds for speed violations, which can be adjusted for both city and non-city areas. These features help provide timely and relevant speed-related notifications based on the user's location and current speed.

> 📝 **INFO**
>
> All speed values in the SDK are provided in **meters per second (mps)**. You may want to convert these to km/h or mph for display purposes using the conversion: `km/h = mps * 3.6` or `mph = mps * 2.237`.

## Configure the speed limit listeners[​](#configure-the-speed-limit-listeners "Direct link to Configure the speed limit listeners")

* Kotlin
* Java

```kotlin
// Kotlin
val alarmListener = UnlAlarmListener.create(
    onHighSpeed = { limit, insideCityArea ->
        val areaType = if (insideCityArea) "inside city area" else "outside city area"
        Log.w("SpeedAlarm", "Speed limit exceeded while $areaType - limit is ${limit.toInt()} mps")
    },
    onSpeedLimit = { speed, limit, insideCityArea ->
        val areaType = if (insideCityArea) "inside city area" else "outside city area"
        Log.i("SpeedAlarm", "New speed limit updated to ${limit.toInt()} mps while $areaType. The current speed is ${speed.toInt()} mps")
    },
    onNormalSpeed = { limit, insideCityArea ->
        val areaType = if (insideCityArea) "inside city area" else "outside city area"
        Log.d("SpeedAlarm", "Normal speed restored while $areaType - limit is ${limit.toInt()} mps")
    }
)

// Create alarm service based on the previously created listener
val alarmService = UnlAlarmService.produce(alarmListener)

```

```java
// Java
UnlAlarmListener alarmListener = UnlAlarmListener.create(
    (Double limit, Boolean insideCityArea) -> {
        String areaType = insideCityArea ? "inside city area" : "outside city area";
        Log.w("SpeedAlarm", "Speed limit exceeded while " + areaType + " - limit is " + limit.intValue() + " mps");
    },
    (Double speed, Double limit, Boolean insideCityArea) -> {
        String areaType = insideCityArea ? "inside city area" : "outside city area";
        Log.i("SpeedAlarm", "New speed limit updated to " + limit.intValue() + " mps while " + areaType + ". The current speed is " + speed.intValue() + " mps");
    },
    (Double limit, Boolean insideCityArea) -> {
        String areaType = insideCityArea ? "inside city area" : "outside city area";
        Log.d("SpeedAlarm", "Normal speed restored while " + areaType + " - limit is " + limit.intValue() + " mps");
    }
);

// Create alarm service based on the previously created listener
UnlAlarmService alarmService = UnlAlarmService.produce(alarmListener);

```

Alternatively, you can extend the `UnlAlarmListener` class for more complex speed monitoring:

* Kotlin
* Java

```kotlin
// Kotlin
class SpeedMonitoringListener : UnlAlarmListener() {

    override fun onHighSpeed(limit: Double, insideCityArea: Boolean) {
        // val limitKmh = (limit * 3.6).toInt() // Convert mps to km/h
        // val areaType = if (insideCityArea) "city" else "highway"

        // Log speed violation
        Log.w("SpeedWarning", "Speed limit exceeded in $areaType: ${limitKmh} km/h")

        // Show user notification
        // showSpeedWarning("Speed limit: ${limitKmh} km/h", insideCityArea)

        // Play audio warning if needed
        // playSpeedWarningSound()
    }

    override fun onSpeedLimit(speed: Double, limit: Double, insideCityArea: Boolean) {
        // val speedKmh = (speed * 3.6).toInt()
        // val limitKmh = (limit * 3.6).toInt()
        // val areaType = if (insideCityArea) "city area" else "highway"
        //
        // updateSpeedLimitDisplay(limitKmh, areaType)

        // Check if currently speeding
        // if (speed > limit) {
        //     showSpeedViolationWarning(speedKmh, limitKmh)
        // }
    }

    override fun onNormalSpeed(limit: Double, insideCityArea: Boolean) {
        // Hide any active speed warnings
        // hideSpeedWarning()

        // Update UI to show normal speed status
        // showNormalSpeedStatus()
    }

    private fun showSpeedWarning(message: String, isCity: Boolean) {
        // Update UI with speed warning
    }

    private fun updateSpeedLimitDisplay(limit: Int, areaType: String) {
        // speedLimitDisplay.text = "${limit} km/h ($areaType)"
    }
}

val alarmService = UnlAlarmService.produce(SpeedMonitoringListener())

```

```java
// Java
class SpeedMonitoringListener extends UnlAlarmListener {

    @Override
    public void onHighSpeed(double limit, boolean insideCityArea) {
        // int limitKmh = (int) (limit * 3.6); // Convert mps to km/h
        // String areaType = insideCityArea ? "city" : "highway";

        // Log speed violation
        Log.w("SpeedWarning", "Speed limit exceeded in " + areaType + ": " + limitKmh + " km/h");

        // Show user notification
        // showSpeedWarning("Speed limit: " + limitKmh + " km/h", insideCityArea);

        // Play audio warning if needed
        // playSpeedWarningSound();
    }

    @Override
    public void onSpeedLimit(double speed, double limit, boolean insideCityArea) {
        // int speedKmh = (int) (speed * 3.6);
        // int limitKmh = (int) (limit * 3.6);
        // String areaType = insideCityArea ? "city area" : "highway";
        //
        // updateSpeedLimitDisplay(limitKmh, areaType);

        // Check if currently speeding
        // if (speed > limit) {
        //     showSpeedViolationWarning(speedKmh, limitKmh);
        // }
    }

    @Override
    public void onNormalSpeed(double limit, boolean insideCityArea) {
        // Hide any active speed warnings
        // hideSpeedWarning();

        // Update UI to show normal speed status
        // showNormalSpeedStatus();
    }

    private void showSpeedWarning(String message, boolean isCity) {
        // Update UI with speed warning
    }

    private void updateSpeedLimitDisplay(int limit, String areaType) {
        // speedLimitDisplay.setText(limit + " km/h (" + areaType + ")");
    }
}

UnlAlarmService alarmService = UnlAlarmService.produce(new SpeedMonitoringListener());

```

The `onHighSpeed` will continuously send notifications while the user exceeds with a given threshold the maximum speed limit for the current road section. The `onSpeedLimit` will be triggered once the current road section has a different speed than the previous road section. The `onNormalSpeed` will be triggered once the user speed becomes within the limit of the maximum speed limit for the current road section.

> 📝 **INFO**
>
> Although the parameter is named `insideCityArea`, it refers to areas with generally lower speed limits, such as cities, towns, villages, or similar settlements, regardless of their classification.

> 🚨 **DANGER**
>
> The `limit` parameter provided to `onSpeedLimit` will be 0 if the matched road section does not have a maximum speed limit available or if no road could be found.

## Set the threshold for speed[​](#set-the-threshold-for-speed "Direct link to Set the threshold for speed")

The threshold for the maximum speed excess that triggers the `onHighSpeed` callback can be configured as follows:

* Kotlin
* Java

```kotlin
// Kotlin
// Set speed thresholds in meters per second (mps)
// Trigger onHighSpeed when the speed limit is exceeded by 1.39 mps (5 km/h) inside a city area
alarmService?.setOverSpeedThreshold(1.39, true)

// Trigger onHighSpeed when the speed limit is exceeded by 2.78 mps (10 km/h) outside city areas
alarmService?.setOverSpeedThreshold(2.78, false)

// Additional examples with different tolerance levels:
// Very strict: 2.78 mps (10 km/h) tolerance for city areas
alarmService?.setOverSpeedThreshold(2.78, true)

// Moderate: 4.17 mps (15 km/h) tolerance for highways
alarmService?.setOverSpeedThreshold(4.17, false)

// Lenient: 5.56 mps (20 km/h) tolerance for highways
alarmService?.setOverSpeedThreshold(5.56, false)

```

```java
// Java
// Set speed thresholds in meters per second (mps)
// Trigger onHighSpeed when the speed limit is exceeded by 1.39 mps (5 km/h) inside a city area
if (alarmService != null) {
    alarmService.setOverSpeedThreshold(1.39, true);

    // Trigger onHighSpeed when the speed limit is exceeded by 2.78 mps (10 km/h) outside city areas
    alarmService.setOverSpeedThreshold(2.78, false);

    // Additional examples with different tolerance levels:
    // Very strict: 2.78 mps (10 km/h) tolerance for city areas
    alarmService.setOverSpeedThreshold(2.78, true);

    // Moderate: 4.17 mps (15 km/h) tolerance for highways
    alarmService.setOverSpeedThreshold(4.17, false);

    // Lenient: 5.56 mps (20 km/h) tolerance for highways
    alarmService.setOverSpeedThreshold(5.56, false);
}

```

## Get the threshold for the speed[​](#get-the-threshold-for-the-speed "Direct link to Get the threshold for the speed")

The configured threshold can be accessed as follows:

* Kotlin
* Java

```kotlin
// Kotlin
val currentThresholdInsideCity = alarmService?.getOverSpeedThreshold(true) ?: 0.0
val currentThresholdOutsideCity = alarmService?.getOverSpeedThreshold(false) ?: 0.0

// Convert to km/h for display
val thresholdCityKmh = (currentThresholdInsideCity * 3.6).toInt()
val thresholdHighwayKmh = (currentThresholdOutsideCity * 3.6).toInt()

println("Speed thresholds: City=${thresholdCityKmh} km/h, Highway=${thresholdHighwayKmh} km/h")

```

```java
// Java
double currentThresholdInsideCity = alarmService != null ? alarmService.getOverSpeedThreshold(true) : 0.0;
double currentThresholdOutsideCity = alarmService != null ? alarmService.getOverSpeedThreshold(false) : 0.0;

// Convert to km/h for display
int thresholdCityKmh = (int) (currentThresholdInsideCity * 3.6);
int thresholdHighwayKmh = (int) (currentThresholdOutsideCity * 3.6);

System.out.println("Speed thresholds: City=" + thresholdCityKmh + " km/h, Highway=" + thresholdHighwayKmh + " km/h");

```

## Complete speed monitoring example[​](#complete-speed-monitoring-example "Direct link to Complete speed monitoring example")

Here's a complete implementation demonstrating speed limit monitoring in an Android application:

* Kotlin
* Java

```kotlin
// Kotlin
class SpeedMonitoringActivity : AppCompatActivity() {

    private var alarmService: UnlAlarmService? = null
    private lateinit var speedLimitText: TextView
    private lateinit var currentSpeedText: TextView
    private lateinit var speedWarningPanel: View

    private val speedAlarmListener = UnlAlarmListener.create(
        onHighSpeed = { limit, insideCityArea ->
            handleSpeedViolation(limit, insideCityArea)
        },
        onSpeedLimit = { speed, limit, insideCityArea ->
            handleSpeedLimitChange(speed, limit, insideCityArea)
        },
        onNormalSpeed = { limit, insideCityArea ->
            handleNormalSpeed(limit, insideCityArea)
        },
        postOnMain = true
    )

    private fun initializeSpeedMonitoring() {
        SdkCall.execute {
            alarmService = UnlAlarmService.produce(speedAlarmListener)
            alarmService?.apply {
                // Set reasonable speed thresholds in meters per second (mps)
                setOverSpeedThreshold(1.39, true)  // 5 km/h tolerance in city areas
                setOverSpeedThreshold(2.78, false) // 10 km/h tolerance on highways

                // Enable monitoring without requiring an active route
                monitorWithoutRoute = true
            }
        }
    }

    private fun handleSpeedViolation(limit: Double, insideCityArea: Boolean) {
        val limitKmh = (limit * 3.6).toInt()
        val areaType = if (insideCityArea) "city area" else "highway"

        // Show warning UI
        speedWarningPanel.visibility = View.VISIBLE
        speedWarningPanel.setBackgroundColor(Color.RED)

        // Update warning text
        val warningText = "SPEED LIMIT EXCEEDED!\nLimit: ${limitKmh} km/h in $areaType"
        Toast.makeText(this, warningText, Toast.LENGTH_SHORT).show()

        // Play warning sound
        playWarningSound()
    }

    private fun handleSpeedLimitChange(speed: Double, limit: Double, insideCityArea: Boolean) {
        val speedKmh = (speed * 3.6).toInt()
        val limitKmh = if (limit > 0) (limit * 3.6).toInt() else 0
        val areaType = if (insideCityArea) "City" else "Highway"

        // Update speed limit display
        val displayText = if (limitKmh > 0) {
            "$limitKmh km/h ($areaType)"
        } else {
            "No limit ($areaType)"
        }
        speedLimitText.text = displayText
        currentSpeedText.text = "${speedKmh} km/h"

        // Update warning state
        if (limit > 0 && speed > limit) {
            speedWarningPanel.setBackgroundColor(Color.YELLOW)
            speedWarningPanel.visibility = View.VISIBLE
        }
    }

    private fun handleNormalSpeed(limit: Double, insideCityArea: Boolean) {
        // Hide warning UI
        speedWarningPanel.visibility = View.GONE

        // Update status
        val limitKmh = if (limit > 0) (limit * 3.6).toInt() else 0
        val statusText = if (limitKmh > 0) {
            "Speed: Normal (limit ${limitKmh} km/h)"
        } else {
            "Speed: Normal (no limit)"
        }
        Toast.makeText(this, statusText, Toast.LENGTH_SHORT).show()
    }

    private fun playWarningSound() {
        // Implementation depends on your audio system
        // Example using MediaPlayer or TTS
    }

    override fun onDestroy() {
        super.onDestroy()
        alarmService?.setAlarmListener(null)
        alarmService = null
    }
}

```

```java
// Java
class SpeedMonitoringActivity extends AppCompatActivity {

    private UnlAlarmService alarmService = null;
    private TextView speedLimitText;
    private TextView currentSpeedText;
    private View speedWarningPanel;

    private final UnlAlarmListener speedAlarmListener = UnlAlarmListener.create(
        (Double limit, Boolean insideCityArea) -> {
            handleSpeedViolation(limit, insideCityArea);
        },
        (Double speed, Double limit, Boolean insideCityArea) -> {
            handleSpeedLimitChange(speed, limit, insideCityArea);
        },
        (Double limit, Boolean insideCityArea) -> {
            handleNormalSpeed(limit, insideCityArea);
        },
        true // postOnMain
    );

    private void initializeSpeedMonitoring() {
        SdkCall.execute(() -> {
            alarmService = UnlAlarmService.produce(speedAlarmListener);
            if (alarmService != null) {
                // Set reasonable speed thresholds in meters per second (mps)
                alarmService.setOverSpeedThreshold(1.39, true);  // 5 km/h tolerance in city areas
                alarmService.setOverSpeedThreshold(2.78, false); // 10 km/h tolerance on highways

                // Enable monitoring without requiring an active route
                alarmService.setMonitorWithoutRoute(true);
            }
        });
    }

    private void handleSpeedViolation(double limit, boolean insideCityArea) {
        int limitKmh = (int) (limit * 3.6);
        String areaType = insideCityArea ? "city area" : "highway";

        // Show warning UI
        speedWarningPanel.setVisibility(View.VISIBLE);
        speedWarningPanel.setBackgroundColor(Color.RED);

        // Update warning text
        String warningText = "SPEED LIMIT EXCEEDED!\nLimit: " + limitKmh + " km/h in " + areaType;
        Toast.makeText(this, warningText, Toast.LENGTH_SHORT).show();

        // Play warning sound
        playWarningSound();
    }

    private void handleSpeedLimitChange(double speed, double limit, boolean insideCityArea) {
        int speedKmh = (int) (speed * 3.6);
        int limitKmh = limit > 0 ? (int) (limit * 3.6) : 0;
        String areaType = insideCityArea ? "City" : "Highway";

        // Update speed limit display
        String displayText;
        if (limitKmh > 0) {
            displayText = limitKmh + " km/h (" + areaType + ")";
        } else {
            displayText = "No limit (" + areaType + ")";
        }
        speedLimitText.setText(displayText);
        currentSpeedText.setText(speedKmh + " km/h");

        // Update warning state
        if (limit > 0 && speed > limit) {
            speedWarningPanel.setBackgroundColor(Color.YELLOW);
            speedWarningPanel.setVisibility(View.VISIBLE);
        }
    }

    private void handleNormalSpeed(double limit, boolean insideCityArea) {
        // Hide warning UI
        speedWarningPanel.setVisibility(View.GONE);

        // Update status
        int limitKmh = limit > 0 ? (int) (limit * 3.6) : 0;
        String statusText;
        if (limitKmh > 0) {
            statusText = "Speed: Normal (limit " + limitKmh + " km/h)";
        } else {
            statusText = "Speed: Normal (no limit)";
        }
        Toast.makeText(this, statusText, Toast.LENGTH_SHORT).show();
    }

    private void playWarningSound() {
        // Implementation depends on your audio system
        // Example using MediaPlayer or TTS
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (alarmService != null) {
            alarmService.setAlarmListener(null);
        }
        alarmService = null;
    }
}

```
