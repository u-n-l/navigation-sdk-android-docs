# Integrate the SDK

This document describes the steps to install the most recent version of the Navigation SDK for Android and configure your Android app to use the SDK.

## Prerequisites[​](#prerequisites "Direct link to Prerequisites")

Download and install Android Studio from the official [Android Developer website](https://developer.android.com/studio).

> 📝 **Info** The procedures described in this document were carried out with Android Studio Narwhal 3 Feature Drop | 2025.1.3.


## Local AAR library

After you download the UNL Navigation SDK for Android `.aar` file then create the following project structure: **your\_project/app/libs/**. Add the `.aar` in the **libs** directory and the following code inside the dependencies block from the module-level `build.gradle.kts` file to include the Navigation SDK for Android:

**Kotlin:**

```
dependencies {
    implementation(files("libs/<AAR_FILE>.aar"))
}
```

**Java:**

```
// In build.gradle (Groovy DSL)
dependencies {
    implementation files('libs/<AAR_FILE>.aar')
}
```

You are now ready to go to the next step: [Implement the application code](/02-Get%20Started/03-Create%20Your%20First%20App.md#implement-the-application-code).
