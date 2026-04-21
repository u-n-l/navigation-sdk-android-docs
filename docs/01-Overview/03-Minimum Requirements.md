# Minimum Requirements
Before beginning development with the UNL Navigation SDK for Android, it is critical to validate your environment against the minimum system requirements. 

Adhering to these specifications ensures baseline compatibility and facilitates optimal runtime performance in location-aware applications. Key factors include supported API levels, Java/Kotlin language features, OpenGL ES compatibility, and device memory configurations.

## Development Machine
UNL Navigation SDK for Android supports the same operating systems as the official Android SDK from Google. Development is generally supported on Apple macOS, Linux and Windows. For details about specific versions, refer to [Google's documentation](/link:https://developer.android.com/studio).


## Software Requirements
- Apps must be built using Android SDK 21 or higher.
- Java and Kotlin are both supported.
- Devices should support OpenGL ES 2.0/3.0.

The SDK has been compiled with support for [16 KB page sizes](/link:https://developer.android.com/guide/practices/page-sizes) using [NDK r27 LTS](/link:https://github.com/android/ndk/wiki/Changelog-r27).

## Target Devices
Compatibility is assured with both real devices and emulators used for development.

The Navigation SDK for Android is available for all [Google supported ABIs](/link:https://developer.android.com/ndk/guides/abis#sa).

### Minimum Supported Hardware Specifications:
- RAM: At least 1 GB memory.
- ROM: At leas 250MB of storage.
- GPU: All GPUs that support OpenGL ES 2.0/3.0.

>  🚨 **Danger**: Please be aware that on lower-end devices, performance may be reduced due to hardware limitations. This can affect the smoothness of the user interface and overall responsiveness of the application, particularly in areas with complex or resource-intensive scenes.
