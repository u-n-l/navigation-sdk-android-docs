# Get started with Alarms

The alarm system within the UnlSdk offers a range of monitoring and notification functionalities. It allows for the detection and management of different types of alarms, such as boundary crossings, speed limit violations, and landmark alerts. The system provides the ability to configure parameters like alarm distance, overspeed thresholds, and whether monitoring should occur even without a route being followed.

The key feature of this system is its ability to monitor specific geographic areas or routes and trigger alarms when predefined conditions, such as crossing a boundary or entering/exiting a tunnel, are met. It also provides customization options for alarm behavior based on location (e.g., inside or outside city limits). Additionally, users can set up callbacks to receive notifications about specific events, including changes in monitoring state or when a landmark alarm is triggered.

The system supports interaction with various alarm types, including overlay item and landmark alarms, and offers an easy interface for both setting and getting alarm-related information.

> 💡 **TIP**
>
> Multiple alarm services and listeners can operate simultaneously, allowing for the monitoring of various events concurrently.

## Configure UnlAlarmService and UnlAlarmListener[​](#configure-alarmservice-and-alarmlistener "Direct link to Configure UnlAlarmService and UnlAlarmListener")

The code snippet provided below defines an `UnlAlarmListener` and creates an `UnlAlarmService` based on this listener:

* Kotlin
* Java

```kotlin
// Kotlin
// Create alarm listener using lambda-based approach
val alarmListener = UnlAlarmListener.create(
    onBoundaryCrossed = {
        // Handle boundary crossing events
        // Use alarmService?.crossedBoundaries to get affected geographic areas
    },
    onMonitoringStateChanged = { isMonitoringActive ->
        // Handle monitoring state changes (GPS availability)
    },
    onTunnelEntered = {
        // Handle tunnel entry events (switch UI to night mode)
    },
    onTunnelLeft = {
        // Handle tunnel exit events (switch UI to day mode)
    },
    onLandmarkAlarmsUpdated = {
        // Handle new landmark alarms
        // Use alarmService?.landmarkAlarms to get active landmark alarms
    },
    onOverlayItemAlarmsUpdated = {
        // Handle new overlay item alarms (e.g., speed cameras)
        // Use alarmService?.overlayItemAlarms to get active overlay alarms
    },
    onLandmarkAlarmsPassedOver = {
        // Handle passed landmark alarms
        // Use alarmService?.landmarkAlarmsPassedOver to get deactivated alarms
    },
    onOverlayItemAlarmsPassedOver = {
        // Handle passed overlay item alarms
        // Use alarmService?.overlayItemAlarmsPassedOver to get deactivated alarms
    },
    onHighSpeed = { limit, insideCityArea ->
        // Handle speed limit exceeded events
    },
    onSpeedLimit = { speed, limit, insideCityArea ->
        // Handle speed limit change events
    },
    onNormalSpeed = { limit, insideCityArea ->
        // Handle return to normal speed events
    },
    onEnterDayMode = {
        // Handle day mode transition
    },
    onEnterNightMode = {
        // Handle night mode transition
    }
)

// Create alarm service based on the previously created listener
val alarmService = UnlAlarmService.produce(alarmListener)

```

```java
// Java
// Create alarm listener using lambda-based approach
UnlAlarmListener alarmListener = UnlAlarmListener.create(
    () -> {
        // Handle boundary crossing events
        // Use alarmService.getCrossedBoundaries() to get affected geographic areas
    },
    (Boolean isMonitoringActive) -> {
        // Handle monitoring state changes (GPS availability)
    },
    () -> {
        // Handle tunnel entry events (switch UI to night mode)
    },
    () -> {
        // Handle tunnel exit events (switch UI to day mode)
    },
    () -> {
        // Handle new landmark alarms
        // Use alarmService.getLandmarkAlarms() to get active landmark alarms
    },
    () -> {
        // Handle new overlay item alarms (e.g., speed cameras)
        // Use alarmService.getOverlayItemAlarms() to get active overlay alarms
    },
    () -> {
        // Handle passed landmark alarms
        // Use alarmService.getLandmarkAlarmsPassedOver() to get deactivated alarms
    },
    () -> {
        // Handle passed overlay item alarms
        // Use alarmService.getOverlayItemAlarmsPassedOver() to get deactivated alarms
    },
    (Double limit, Boolean insideCityArea) -> {
        // Handle speed limit exceeded events
    },
    (Double speed, Double limit, Boolean insideCityArea) -> {
        // Handle speed limit change events
    },
    (Double limit, Boolean insideCityArea) -> {
        // Handle return to normal speed events
    },
    () -> {
        // Handle day mode transition
    },
    () -> {
        // Handle night mode transition
    }
);

// Create alarm service based on the previously created listener
UnlAlarmService alarmService = UnlAlarmService.produce(alarmListener);

```

Alternatively, you can extend the `UnlAlarmListener` class directly for more complex implementations:

* Kotlin
* Java

```kotlin
// Kotlin
class CustomAlarmListener : UnlAlarmListener() {
    override fun onBoundaryCrossed() {
        // Handle boundary crossing
    }

    override fun onMonitoringStateChanged(isMonitoringActive: Boolean) {
        // Handle monitoring state changes
    }

    override fun onHighSpeed(limit: Double, insideCityArea: Boolean) {
        // Handle speed limit violations
    }

    // Override other methods as needed
}

val alarmService = UnlAlarmService.produce(CustomAlarmListener())

```

```java
// Java
class CustomAlarmListener extends UnlAlarmListener {
    @Override
    public void onBoundaryCrossed() {
        // Handle boundary crossing
    }

    @Override
    public void onMonitoringStateChanged(boolean isMonitoringActive) {
        // Handle monitoring state changes
    }

    @Override
    public void onHighSpeed(double limit, boolean insideCityArea) {
        // Handle speed limit violations
    }

    // Override other methods as needed
}

UnlAlarmService alarmService = UnlAlarmService.produce(new CustomAlarmListener());

```

Each callback method listed above can be specified to receive notifications about different events or topics, depending on your needs. These events cover a range of scenarios, such as boundary crossings, monitoring state changes, tunnel entries or exits, speed limits, and more. By customizing the callbacks, you can tailor the notifications to suit specific use cases, ensuring that the system responds appropriately to various triggers or conditions.

> 🚨 **DANGER**
>
> The `UnlAlarmListener` and `UnlAlarmService` objects must remain in memory for the duration of the notification period. If these objects are removed, the callbacks will not be triggered. It's recommended to keep the `UnlAlarmListener` and `UnlAlarmService` variables in a class that is alive during the whole session.

## Change the alarm listener[​](#change-the-alarm-listener "Direct link to Change the alarm listener")

The alarm listener associated with the alarm service can be updated at any time, allowing for dynamic configuration and flexibility in handling various notifications.

* Kotlin
* Java

```kotlin
// Kotlin
val newAlarmListener = UnlAlarmListener.create(
    onHighSpeed = { limit, insideCityArea ->
        // New implementation for speed alerts
    }
)
alarmService?.setAlarmListener(newAlarmListener)

```

```java
// Java
UnlAlarmListener newAlarmListener = UnlAlarmListener.create(
    (Double limit, Boolean insideCityArea) -> {
        // New implementation for speed alerts
    }
);
if (alarmService != null) {
    alarmService.setAlarmListener(newAlarmListener);
}

```
