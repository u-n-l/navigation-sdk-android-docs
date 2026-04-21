# Other alarms

The Navigation SDK for Android provides advanced notification capabilities, enabling users to receive alerts for significant environmental changes. This includes notifications for entering and exiting tunnels, as well as transitions between day and night, based on the user's current location and time of year.

## Get notified when entering and exiting tunnels[​](#get-notified-when-entering-and-exiting-tunnels "Direct link to Get notified when entering and exiting tunnels")

This snippet below demonstrates how to set up notifications for entering and exiting tunnels using the UnlAlarmListener:

* Kotlin
* Java

```kotlin
// Kotlin
val alarmListener = UnlAlarmListener.create {
    onTunnelEntered {
        Log.d("TunnelAlarm", "Tunnel entered")
        // Update UI for tunnel mode (e.g., switch to night theme)
    }
    onTunnelLeft {
        Log.d("TunnelAlarm", "Tunnel left")
        // Update UI for normal mode (e.g., switch back to day theme)
    }
}

```

```java
// Java
UnlAlarmListener alarmListener = UnlAlarmListener.create(
    () -> {
        Log.d("TunnelAlarm", "Tunnel entered");
        // Update UI for tunnel mode (e.g., switch to night theme)
    },
    () -> {
        Log.d("TunnelAlarm", "Tunnel left");
        // Update UI for normal mode (e.g., switch back to day theme)
    }
);

```

## Get notified when transitioning between day and night[​](#get-notified-when-transitioning-between-day-and-night "Direct link to Get notified when transitioning between day and night")

The Navigation SDK for Android offers functionality to notify users when their location transitions between day and night, based on the geographical region and the corresponding seasonal changes:

* Kotlin
* Java

```kotlin
// Kotlin
val alarmListener = UnlAlarmListener.create {
    onEnterDayMode {
        Log.d("DayNightAlarm", "Day mode entered")
        // Update UI for day mode (e.g., light theme)
    }
    onEnterNightMode {
        Log.d("DayNightAlarm", "Night mode entered")
        // Update UI for night mode (e.g., dark theme)
    }
}

```

```java
//Java
UnlAlarmListener alarmListener = UnlAlarmListener.create(
    () -> {
        Log.d("DayNightAlarm", "Day mode entered");
        // Update UI for day mode (e.g., light theme)
    },
    () -> {
        Log.d("DayNightAlarm", "Night mode entered");
        // Update UI for night mode (e.g., dark theme)
    }
);

```
