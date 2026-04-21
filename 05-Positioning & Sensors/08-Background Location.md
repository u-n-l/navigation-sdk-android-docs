# Background Location

Some use cases require location access even when the app is in the background. In such cases, you'll need to configure the Android platform appropriately. The SDK supports this scenario, but platform permissions and services must be correctly set up for it to work.

We recommend enabling background location support if your application includes features like recording, navigation, or content download (especially for maps, which can be quite large and may take a while to fetch).

## Android Setup[​](#android-setup "Direct link to Android Setup")

You'll need to declare permissions in your manifest, and then implement a foreground service to keep location updates alive.

#### Required AndroidManifest Changes[​](#required-androidmanifest-changes "Direct link to Required AndroidManifest Changes")

Make sure you include the necessary permissions and service declarations in your `AndroidManifest.xml`.

```xml
<uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION" />
<!-- Needed for foreground service -->
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_LOCATION" />
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />

```

> 💡 **TIP**
>
> On Android 10+ (API level 29+), you also need to request **ACCESS\_BACKGROUND\_LOCATION** explicitly at runtime. Make sure to handle this in your app's permission request flow.

> 🚨 **DANGER**
>
> You should create a foreground service on Android to ensure that location updates continue when the app is in the background. Failing to do so may result in the operating system terminating your app's background processes, leading to loss of location updates.

#### Foreground Service Implementation[​](#foreground-service-implementation "Direct link to Foreground Service Implementation")

Android won't let you run background location tracking unless you create a foreground service. Here you can find more about foreground services: [Background Location Example](https://developer.android.com/guide/components/foreground-services).

## Background Location Configuration[​](#background-location-configuration "Direct link to Background Location Configuration")

To enable background location within the Android SDK, you need to set the `AllowsBackgroundLocationUpdates` configuration key to true in your Kotlin code.

* Kotlin
* Java

```kotlin
// Kotlin
val dataSource = DataSourceFactory.produceLive()
    ?: throw IllegalStateException("Error creating data source")

// Configure background location preferences
val preferences = ParameterList()
preferences.add(Parameter(key = ESConfigKeys.Position.AllowsBackgroundLocationUpdates, value = "true"))

val err = dataSource.setPreferences(EDataType.Position, preferences)

if (err != UnlError.NoError) {
    throw IllegalStateException("Error setting data source preferences: $err")
}

```

```java
// Java
DataSource dataSource = DataSourceFactory.produceLive();
if (dataSource == null) {
    throw new IllegalStateException("Error creating data source");
}

// Configure background location preferences
ParameterList preferences = new ParameterList();
preferences.add(new Parameter(ESConfigKeys.Position.AllowsBackgroundLocationUpdates, "true"));

int err = dataSource.setPreferences(EDataType.Position, preferences);

if (err != UnlError.NoError) {
    throw new IllegalStateException("Error setting data source preferences: " + err);
}

```
