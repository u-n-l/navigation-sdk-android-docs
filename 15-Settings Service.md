# Settings Service

The UNL Navigation SDK for Android provides functionality for storing key-value pairs in permanent storage. This is managed via the `UnlSettingsService` class.

The settings are saved within a `.ini` file.

## Create a Settings Service[​](#create-a-settings-service "Direct link to Create a Settings Service")

You can create or open a settings storage using the factory method of `UnlSettingsService`. If no path is provided, a default one will be used:

* Kotlin
* Java

```kotlin
// Kotlin
val settings = UnlSettingsService.produce(null)

```

```java
// Java
UnlSettingsService settings = UnlSettingsService.produce(null);

```

Or a custom path can be provided:

* Kotlin
* Java

```kotlin
// Kotlin
val settings = UnlSettingsService.produce("/custom/settings/path")

```

```java
// Java
UnlSettingsService settings = UnlSettingsService.produce("/custom/settings/path");

```

You can also access the current file path where the settings are stored:

* Kotlin
* Java

```kotlin
// Kotlin
val currentPath = settings?.path

```

```java
// Java
String currentPath = settings != null ? settings.getPath() : null;

```

## Add and get values[​](#add-and-get-values "Direct link to Add and get values")

You can store various types of data using appropriate set methods:

* Kotlin
* Java

```kotlin
// Kotlin
settings?.setStringValue("username", "john_doe")
settings?.setBooleanValue("isLoggedIn", true)
settings?.setIntValue("launchCount", 5)
settings?.setLongValue("highScore", 1234567890123L)
settings?.setDoubleValue("volume", 0.75)

```

```java
// Java
if (settings != null) {
    settings.setStringValue("username", "john_doe");
    settings.setBooleanValue("isLoggedIn", true);
    settings.setIntValue("launchCount", 5);
    settings.setLongValue("highScore", 1234567890123L);
    settings.setDoubleValue("volume", 0.75);
}

```

To retrieve values, use the corresponding get methods. These methods take an optional `defaultValue` parameter which is returned when the key could not be found in the selected group. The `defaultValue` does not set the value.

* Kotlin
* Java

```kotlin
// Kotlin
val username = settings?.getStringValue("username", "guest")
val isLoggedIn = settings?.getBooleanValue("isLoggedIn", false)
val launchCount = settings?.getIntValue("launchCount", 0)
val highScore = settings?.getLongValue("highScore", 0L)
val volume = settings?.getDoubleValue("volume", 1.0)

```

```java
// Java
String username = settings != null ? settings.getStringValue("username", "guest") : "guest";
boolean isLoggedIn = settings != null ? settings.getBooleanValue("isLoggedIn", false) : false;
int launchCount = settings != null ? settings.getIntValue("launchCount", 0) : 0;
long highScore = settings != null ? settings.getLongValue("highScore", 0L) : 0L;
double volume = settings != null ? settings.getDoubleValue("volume", 1.0) : 1.0;

```

When the set is made on a type and the get is made on another type, a conversion is done. For example:

* Kotlin
* Java

```kotlin
// Kotlin
settings?.setIntValue("count", 1234)

val value = settings?.getStringValue("count") // Returns "1234"

```

```java
// Java
if (settings != null) {
    settings.setIntValue("count", 1234);
}

String value = settings != null ? settings.getStringValue("count") : null; // Returns "1234"

```

> 💡 **TIP**
>
> Each change may take up to one second to be written to storage. Use the `flush` method to ensure the changes are written to permanent storage.

## Groups[​](#groups "Direct link to Groups")

Groups allow you to organize settings in logical units. The default group is `DEFAULT`. Only one group can be active at a time, and nested groups are not allowed.

* Kotlin
* Java

```kotlin
// Kotlin
// All operations above this are made inside DEFAULT
settings?.beginGroup("USER_PREFERENCES")

// All operations here are made inside USER_PREFERENCES

settings?.beginGroup("OTHER_SETTINGS")

// All operations here are made inside OTHER_SETTINGS

settings?.endGroup()
//All operations below this are made inside DEFAULT

```

```java
// Java
// All operations above this are made inside DEFAULT
if (settings != null) {
    settings.beginGroup("USER_PREFERENCES");
}

// All operations here are made inside USER_PREFERENCES

if (settings != null) {
    settings.beginGroup("OTHER_SETTINGS");
}

// All operations here are made inside OTHER_SETTINGS

if (settings != null) {
    settings.endGroup();
}
// All operations below this are made inside DEFAULT

```

In order to get the current group use the `groupName` property:

* Kotlin
* Java

```kotlin
// Kotlin
val currentGroup = settings?.groupName

```

```java
// Java
String currentGroup = settings != null ? settings.getGroupName() : null;

```

> 🚨 **DANGER**
>
> The values passed to `beginGroup` are converted to upper-case.

> 💡 **TIP**
>
> A `flush` is automatically done after the group is changed.

## Remove values[​](#remove-values "Direct link to Remove values")

### Remove value by key[​](#remove-value-by-key "Direct link to Remove value by key")

The `remove` method takes the key (or a pattern) and returns the number of deleted entries from the current group.

* Kotlin
* Java

```kotlin
// Kotlin
val removedCount = settings?.remove("username")

```

```java
// Java
int removedCount = settings != null ? settings.remove("username") : 0;

```

### Clear all values[​](#clear-all-values "Direct link to Clear all values")

Use the `clear` method which removes all the settings from all the groups.

* Kotlin
* Java

```kotlin
// Kotlin
settings?.clear()

```

```java
// Java
if (settings != null) {
    settings.clear();
}

```
