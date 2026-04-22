# Create your first app

This document describes the steps to create a new Android application that uses the Navigation SDK for Android. You will create a simple application that displays a map.

This tutorial has 4 parts:

* Create and use a Service API key
* Create an Android project
* Integrate the Navigation SDK for Android
* Implement the application code

## Prerequisites[​](#prerequisites "Direct link to Prerequisites")

* You have Android Studio installed.
* You have a basic understanding of Android development and Kotlin programming language.

### Create and use a Service API key[​](#create-and-use-an-api-key "Direct link to Create and use an API key")

Follow our [step-by-step guide on UNL Platform](https://platform.unl.global/developer_portal/docs?page=create_service_account) to create a Service Account and generate your API key and start building your Android solution.

>   🚨 **Danger**
>
> If the API key is not configured, some features will be restricted, and a watermark will appear on the map. Functionality such as map downloads, updates, and other features may be unavailable or may not function as expected. Please ensure the Service Key is set correctly. Ensure the API key is stored securely and protected against unauthorized access or exposure.

The key must be put in the `AndroidManifest.xml` file, inside the `<application>` tag. Replace `<token>` with your actual Service Key.

```xml
<meta-data
    android:name="com.unl.sdk.token"
    android:value="<your_token_here>"/>

```

## Create an Android project[​](#create-an-android-project "Direct link to Create an Android project")

1. Open Android Studio and create a new project by selecting<br />`File` > `New` > `New Project...`.

2. **For Android Views project structure**:

   Select the `No Activity` template and click `Next`.

   **For Jetpack Compose project structure**:

   Select the `Empty Activity` template and click `Next`.

3. Enter a name for your application, such as `UNLHelloMap`.

4. Set the package name to `com.example.unlhellomap`.

5. Choose a save location, and select `Kotlin` as the language.

6. Set the minimum API level to `API 21: Android 5.0 (Lollipop)` or higher.

7. Click `Finish` to create the project.


**Create new project**

## Integrate the Navigation SDK for Android[​](#integrate-the-maps-sdk-for-android "Direct link to Integrate the Navigation SDK for Android")

See the [Integrate the SDK](../02-Get%20Started/02-Integrate%20the%20SDK.md) guide to add the SDK to your project.

## Implement the application code[​](#implement-the-application-code "Direct link to Implement the application code")

Let's create a simple app that initializes the SDK engine and displays a map.

### For Android Views project structure[​](#for-android-views-project-structure "Direct link to For Android Views project structure")

Create a new empty Activity named `MainActivity.kt`. Right click on the package name from the Project window then select `New > Activity > Empty Views Activity`. Replace its content with the following code:

* Kotlin
* Java

```kotlin
// Kotlin
package com.example.unlhellomap

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import androidx.lifecycle.lifecycleScope
import com.example.unlhellomap.databinding.ActivityMainBinding
import com.unlmap.sdk.core.UnlError
import com.unlmap.sdk.core.UnlSdk
import kotlinx.coroutines.launch

class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        ViewCompat.setOnApplyWindowInsetsListener(binding.main) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        // Set map ready callback before SDK init
        binding.unlMapSurface.onDefaultMapViewCreated = { mapView ->
            // mapView is ready — attach controllers or configure map here
        }

        // initSdkWithDefaults is suspend — must run in a coroutine
        lifecycleScope.launch {
            val errorCode = try {
                UnlSdk.initSdkWithDefaults(this@MainActivity)
            } catch (e: Exception) {
                finish()
                return@launch
            }

            if (errorCode != UnlError.NoError) {
                finish()
                return@launch
            }
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        UnlSdk.release()
    }
}

```

```java
// Java
package com.example.unlhellomap;

import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;
import com.example.unlhellomap.databinding.ActivityMainBinding;
import com.unlmap.sdk.core.UnlError;
import com.unlmap.sdk.core.UnlSdk;
import kotlin.Unit;
import kotlin.coroutines.EmptyCoroutineContext;
import kotlinx.coroutines.BuildersKt;

public class MainActivity extends AppCompatActivity {

    private ActivityMainBinding binding;
    private final Handler mainHandler = new Handler(Looper.getMainLooper());

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        ViewCompat.setOnApplyWindowInsetsListener(binding.main, (v, insets) -> {
            var systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        // Set map callback before init
        binding.unlMapSurface.setOnDefaultMapViewCreated(mapView -> {
            // mapView ready — attach controllers here
            return Unit.INSTANCE;
        });

        // initSdkWithDefaults is a Kotlin suspend function.
        // Call it via BuildersKt.runBlocking on a background thread
        // so the main thread is never blocked.
        new Thread(() -> {
            try {
                int errorCode = (int) BuildersKt.runBlocking(
                    EmptyCoroutineContext.INSTANCE,
                    (scope, continuation) ->
                        UnlSdk.INSTANCE.initSdkWithDefaults(MainActivity.this, continuation)
                );

                if (errorCode != UnlError.NoError) {
                    mainHandler.post(this::finish);
                }
            } catch (InterruptedException | ClassCastException e) {
                mainHandler.post(this::finish);
            }
        }).start();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        UnlSdk.INSTANCE.release();
    }
}

```

In the above code, we initialize the SDK engine with default parameters using `UnlSdk.initSdkWithDefaults(this)`. We also set up a callback to handle the case where the API token (Service Key) is rejected. Finally, we release the SDK in the `onDestroy` method.

Replace the content `of R.layout.activity_main` with the following layout file `res/layout/activity_main.xml`:

```xml
?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.unlmap.sdk.core.UnlMapSurfaceView
        android:id="@+id/unl_map_surface"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</androidx.constraintlayout.widget.ConstraintLayout>

```


In the above layout, we added a `UnlMapSurfaceView` which is the view that will display the map.

> 📝 **Info**
>
> `UnlMapSurfaceView` will initialize the SDK engine with default parameters if it is not already initialized.

In the `AndroidManifest.xml` you need to make the `MainActivity` the default activity in the application like this:

```xml
<activity android:name=".MainActivity" android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>

```

### For Jetpack Compose project structure[​](#for-jetpack-compose-project-structure "Direct link to For Jetpack Compose project structure")

When you created the project using the `Empty Activity` template, a `MainActivity.kt` file was automatically created for you. Replace its content with the following code:

* Kotlin
* Java

```kotlin
// Kotlin
package com.example.unlhellomap

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Scaffold
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.viewinterop.AndroidView
import androidx.lifecycle.lifecycleScope
import com.unlmap.sdk.core.UnlError
import com.unlmap.sdk.core.UnlMapSurfaceView
import com.unlmap.sdk.core.UnlSdk
import kotlinx.coroutines.launch

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()

        // initSdkWithDefaults is a suspend function — must run in a coroutine
        lifecycleScope.launch {
            val errorCode = try {
                UnlSdk.initSdkWithDefaults(this@MainActivity)
                // Or pass token directly: UnlSdk.initSdkWithDefaults(this@MainActivity, "YOUR_TOKEN")
            } catch (e: Exception) {
                finish()
                return@launch
            }

            if (errorCode != UnlError.NoError) {
                finish()
                return@launch
            }

            setContent {
                Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
                    UnlMap(Modifier.padding(innerPadding))
                }
            }
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        UnlSdk.release()
    }
}

@Composable
fun UnlMap(modifier: Modifier = Modifier) {
    AndroidView(
        modifier = modifier,
        factory = { context ->
            UnlMapSurfaceView(context, null).apply {
                onDefaultMapViewCreated = { mapView ->
                    // mapView is ready — attach ViewModels or configure map here
                }
            }
        }
    )
}

```

```java
// Java
import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import androidx.activity.ComponentActivity;
import androidx.activity.EdgeToEdge;
import com.unlmap.sdk.core.UnlError;
import com.unlmap.sdk.core.UnlMapSurfaceView;
import com.unlmap.sdk.core.UnlSdk;
import kotlin.Unit;
import kotlin.coroutines.EmptyCoroutineContext;
import kotlinx.coroutines.BuildersKt;

public class MainActivity extends ComponentActivity {

    private final Handler mainHandler = new Handler(Looper.getMainLooper());

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);

        // initSdkWithDefaults is a Kotlin suspend function.
        // Call via BuildersKt.runBlocking on a background thread — never block main.
        new Thread(() -> {
            try {
                int errorCode = (int) BuildersKt.runBlocking(
                    EmptyCoroutineContext.INSTANCE,
                    (scope, continuation) ->
                        UnlSdk.INSTANCE.initSdkWithDefaults(MainActivity.this, continuation)
                );

                if (errorCode != UnlError.NoError) {
                    mainHandler.post(this::finish);
                    return;
                }

                // Init succeeded — create and show the map on the main thread,
                // mirroring the Kotlin version's setContent { ... } call.
                mainHandler.post(() -> {
                    UnlMapSurfaceView mapSurface = new UnlMapSurfaceView(MainActivity.this, null);
                    mapSurface.setOnDefaultMapViewCreated(mapView -> {
                        // mapView is ready — attach ViewModels or configure map here
                        return Unit.INSTANCE;
                    });
                    setContentView(mapSurface);
                });

            } catch (InterruptedException | ClassCastException e) {
                mainHandler.post(this::finish);
            }
        }).start();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        UnlSdk.INSTANCE.release();
    }
}

```

The Composable function named `GEMMap` will display the map using `GemComposeView`. We initialize the SDK engine with default parameters and release the SDK in the `onDestroy` method.

## Final steps[​](#final-steps "Direct link to Final steps")

### Add necessary permissions and run the app[​](#add-necessary-permissions-and-run-the-app "Direct link to Add necessary permissions and run the app")

>  ⚠️ **Warning**
>
> Make sure you have the necessary permissions declared in you AndroidManifest file and internet connection to load the map tiles! <br/>
> Add these network permissions to your `AndroidManifest.xml` file before the `<application>` tag in order to access the network:
>```xml
><uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
><uses-permission android:name="android.permission.INTERNET" />
>
>```
>
> For more information on this topic see **[how to declare permissions in Android](https://developer.android.com/training/permissions/declaring)** and **[how to connect to the network](https://developer.android.com/develop/connectivity/network-ops/connecting)**.

Run the app on an emulator or a physical device. You should see a map displayed on the screen with a watermark text. In order to get rid of it, you must also use an API key.

### What's next?[​](#whats-next "Direct link to What's next?")

Explore more features in the Guides next section.
