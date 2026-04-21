# Add voice guidance

Voice guidance in the Navigation SDK for Android allows you to enhance navigation experiences with spoken instructions. This guide covers how to enable built-in Text-to-Speech (TTS), manage voice settings, switch voices and languages, and integrate custom playback using the `onNavigationSound` callback for maximum flexibility.

The Navigation SDK for Android provides two options for instruction playback:

* **Built-in solutions** - playback using human voice recordings or computer-generated TTS.
* **External integration** - manual handling of sound playback through the `onNavigationSound` callback.

The built-in solution also provides automatic audio session management, ducking other playbacks (such as music) while instructions are playing.

## Quick start[​](#quick-start "Direct link to Quick start")

Enable voice guidance using the built-in `UnlSoundPlayingService` by implementing proper sound handling in your navigation listener:

* Kotlin
* Java

```kotlin
// Kotlin
import com.unlmap.sdk.core.UnlSoundPlayingService
import com.unlmap.sdk.core.UnlSoundPlayingListener
import com.unlmap.sdk.routesandnavigation.UnlNavigationListener

// Create a sound listener for navigation sounds
val soundListener = object : UnlSoundPlayingListener() {
    override fun notifyStart(hasProgress: Boolean) {
        // Navigation sound started
    }

    override fun notifyComplete(errorCode: Int, hint: String) {
        // Navigation sound completed
    }
}

// Add navigation listener with sound handling
val navigationListener = UnlNavigationListener.create(
    onNavigationSound = { sound ->
        // Play the navigation sound using the service
        val preferences = UnlSoundPlayingService.getPlayingPreferences()
        UnlSoundPlayingService.play(sound, soundListener, preferences)
    }
    // Handle other navigation events...
)

// Start navigation with voice guidance
val navigationService = UnlNavigationService()
navigationService.startNavigationWithRoute(
    route = route,
    navigationListener = navigationListener,
    progressListener = UnlProgressListener.create()
)

```

```java
// Java
import com.unlmap.sdk.core.UnlSoundPlayingService;
import com.unlmap.sdk.core.UnlSoundPlayingListener;
import com.unlmap.sdk.routesandnavigation.UnlNavigationListener;

// Create a sound listener for navigation sounds
UnlSoundPlayingListener soundListener = new UnlSoundPlayingListener() {
    @Override
    public void notifyStart(boolean hasProgress) {
        // Navigation sound started
    }

    @Override
    public void notifyComplete(int errorCode, String hint) {
        // Navigation sound completed
    }
};

// Add navigation listener with sound handling
UnlNavigationListener navigationListener = UnlNavigationListener.create(
    /* onNavigationSound */ (sound) -> {
        // Play the navigation sound using the service
        UnlSoundPlayingPreferences preferences = UnlSoundPlayingService.getPlayingPreferences();
        UnlSoundPlayingService.play(sound, soundListener, preferences);
    }
    // Handle other navigation events...
);

// Start navigation with voice guidance
UnlNavigationService navigationService = new UnlNavigationService();
navigationService.startNavigationWithRoute(
    route,
    navigationListener,
    UnlProgressListener.create()
);

```

For external TTS integration, override the default sound handling:

* Kotlin
* Java

```kotlin
// Kotlin
val navigationListener = UnlNavigationListener.create(
    onNavigationSound = { sound ->
        // Custom TTS handling
        val instructionText = sound.getAsString()
        if (instructionText.isNotEmpty()) {
            // Use your preferred TTS engine
            customTtsEngine.speak(instructionText)
        }
    }
    // Handle other events...
)

```

```java
// Java
UnlNavigationListener navigationListener = UnlNavigationListener.create(
    /* onNavigationSound */ (sound) -> {
        // Custom TTS handling
        String instructionText = sound.getAsString();
        if (instructionText != null && !instructionText.isEmpty()) {
            // Use your preferred TTS engine
            customTtsEngine.speak(instructionText);
        }
    }
    // Handle other events...
);

```

> **Note:** The SDK does not provide automatic sound playback. You must implement sound handling in the `onNavigationSound` callback to hear voice instructions.

## Basic Voice Guidance[​](#basic-voice-guidance "Direct link to Basic Voice Guidance")

To enable voice guidance, you need to handle navigation sounds in your `UnlNavigationListener` and use the `UnlSoundPlayingService` to play them:

* Kotlin
* Java

```kotlin
// Kotlin
import com.unlmap.sdk.core.UnlSoundPlayingService
import com.unlmap.sdk.core.UnlSoundPlayingListener
import com.unlmap.sdk.core.UnlSoundPlayingPreferences

// Configure sound preferences
val preferences = UnlSoundPlayingService.getPlayingPreferences()
preferences.volume = 8 // Set volume (0-10)

// Create navigation listener with sound handling
val navigationListener = UnlNavigationListener.create(
    onNavigationSound = { sound ->
        val soundListener = object : UnlSoundPlayingListener() {
            override fun notifyComplete(errorCode: Int, hint: String) {
                if (errorCode != 0) {
                    println("UnlSound playback failed: $errorCode")
                }
            }
        }

        // Play the navigation instruction sound
        UnlSoundPlayingService.play(sound, soundListener, preferences)
    }
)

```

```java
// Java
import com.unlmap.sdk.core.UnlSoundPlayingService;
import com.unlmap.sdk.core.UnlSoundPlayingListener;
import com.unlmap.sdk.core.UnlSoundPlayingPreferences;

// Configure sound preferences
UnlSoundPlayingPreferences preferences = UnlSoundPlayingService.getPlayingPreferences();
preferences.setVolume(8); // Set volume (0-10)

// Create navigation listener with sound handling
UnlNavigationListener navigationListener = UnlNavigationListener.create(
    /* onNavigationSound */ (sound) -> {
        UnlSoundPlayingListener soundListener = new UnlSoundPlayingListener() {
            @Override
            public void notifyComplete(int errorCode, String hint) {
                if (errorCode != 0) {
                    System.out.println("UnlSound playback failed: " + errorCode);
                }
            }
        };

        // Play the navigation instruction sound
        UnlSoundPlayingService.play(sound, soundListener, preferences);
    }
);

```

> ⚠️ **WARNING**
>
> Ensure that a valid TTS voice is configured and the voice volume is set to a positive value within the allowed range (0-10) to hear voice instructions.

> 💡 **TIP**
>
> By default, the current voice is set to the best computer TTS voice matching the default SDK language.

## The UnlSound Playing Service[​](#the-sound-playing-service "Direct link to The UnlSound Playing Service")

The `UnlSoundPlayingService` provides the following key methods for sound management:

| Method                    | Description                                    |
| ----------------------------------------------------- | ------------------------------------------------------------- |
| `play(sound, listener, preferences)`                  | Plays an ISound object with monitoring and custom preferences |
| `playText(text, listener, preferences)`               | Plays TTS text using the current TTS voice                    |
| `playFile(filePath, mimeType, listener, preferences)` | Plays an audio file with specified MIME type                  |
| `getPlayingPreferences()`                             | Gets the current sound playing preferences (volume, etc.)     |
| `setTTSLanguage(languageCode)`                        | Sets the TTS language for computer voices                     |
| `getTTSLanguages()`                                   | Gets list of available TTS languages                          |
| `cancel(listener)`                                    | Cancels playback associated with a specific listener          |
| `playingSoundsCount()`                                | Returns the number of sounds currently playing                |

## Voice[​](#voice "Direct link to Voice")

The Navigation SDK for Android provides a list of voices for each supported language. These voices can be downloaded and activated to provide navigation prompts such as turn instructions, warnings, and announcements.

The SDK offers two types of voice guidance, defined by the `EVoiceType` enum:

* `EVoiceType.Human`: Uses pre-recorded human voices to deliver instructions in a friendly and natural tone. These voices support only basic instruction types and **do not** include road or settlement names.
* `EVoiceType.Computer`: Leverages the device's Text-to-Speech (TTS) engine to provide more detailed and flexible guidance. These voices **fully support** street and place names in the spoken instructions. The quality and availability is dependent on the device.

### Voice Structure[​](#voice-structure "Direct link to Voice Structure")

The `Voice` class provides the following details:

| Property   | Type       | Description                                                                       |
| ---------- | ---------- | --------------------------------------------------------------------------------- |
| `id`       | Long       | Unique identifier for the voice.                                                  |
| `name`     | String     | Display name of the voice.                                                        |
| `filename` | String     | File name which can be used to load the voice (available for `EVoiceType.Human`). |
| `language` | UnlLanguage   | Associated language object.                                                       |
| `type`     | EVoiceType | Enum: `Human` or `Computer`.                                                      |

> 🚨 **DANGER**
>
> Do not confuse the `Voice` and `UnlLanguage` concepts.
>
> The `UnlLanguage` defines **what** is said - it determines the words, phrasing, and localization. The `Voice` defines **how** it is said - it controls attributes like accent, tone, and gender. Always ensure that the selected `Voice` is compatible with the chosen `UnlLanguage`, as mismatched combinations may result in unnatural or incorrect pronunciation.
>
>#### Relevance[​](#relevance "Direct link to Relevance")
>
>* `UnlLanguage` is relevant for both the built-in TTS system and custom solutions using the `onNavigationSound` callback. See the [internationalization guide](/01-Overview/04-Todo.md) for more info about the `UnlLanguage` class.
>* `Voice` is relevant **only** for the built-in voice-guidance (using human and computer voices) playback.

> 🚨 **DANGER**
>
> The SDK distinguishes between two language settings:
>
>* **SDK language** (`UnlSdkSettings.language`) : Defines the language used for all on-screen text and UI intended strings.
>* **Voice language** (`Voice.language`) : Defines the language used for spoken output, whether through the built-in engine (computer/human voices) or via the `onNavigationSound` callback.
>
> Both settings use the same `UnlLanguage` class. The synchronization between the SDK language and the voice language should be made by the user, depending on the use case.

### Get the Current Voice[​](#get-the-current-voice "Direct link to Get the Current Voice")

Use the `voice` property provided by the `UnlSdkSettings` class:

* Kotlin
* Java

```kotlin
// Kotlin
val currentVoice = UnlSdkSettings.voice

```

```java
// Java
Voice currentVoice = UnlSdkSettings.getVoice();

```

### Get the List of Available Human Voices[​](#get-the-list-of-available-human-voices "Direct link to Get the List of Available Human Voices")

The available human voices list is provided by the `getLocalContentList` method provided by the `ContentStore` class.

* Kotlin
* Java

```kotlin
// Kotlin
val contentStore = ContentStore()
val items = contentStore.getLocalContentList(EContentType.HumanVoice)

items?.forEach { contentStoreItem ->
    // The voice name (ex: Ella)
    val name = contentStoreItem.name

    // The absolute path to the voice file. Used for applying the voice.
    val filePath = contentStoreItem.filename

    // Unique identifier for the voice
    val id = contentStoreItem.id

    // Voice language information
    val language = contentStoreItem.language

    // Voice type (Human or Computer)
    val type = contentStoreItem.type

    // Country codes associated with this voice
    val countryCodes = contentStoreItem.countryCodes
}

```

```java
// Java
ContentStore contentStore = new ContentStore();
List<ContentStoreItem> items = contentStore.getLocalContentList(EContentType.HumanVoice);

if (items != null) {
    for (ContentStoreItem contentStoreItem : items) {
        // The voice name (ex: Ella)
        String name = contentStoreItem.getName();

        // The absolute path to the voice file. Used for applying the voice.
        String filePath = contentStoreItem.getFilename();

        // Unique identifier for the voice
        long id = contentStoreItem.getId();

        // Voice language information
        UnlLanguage language = contentStoreItem.getLanguage();

        // Voice type (Human or Computer)
        EVoiceType type = contentStoreItem.getType();

        // Country codes associated with this voice
        List<String> countryCodes = contentStoreItem.getCountryCodes();
    }
}

```

> 💡 **TIP**
>
> Check the [Manage Content Guide](/01-Overview/04-Todo.md) for information about managing voices and performing operations such as downloading, deleting, and more and details about the `ContentStore` and `ContentStoreItem` classes.

### Apply a Voice by path[​](#apply-a-voice-by-path "Direct link to Apply a Voice by path")

A human voice can be applied by providing the absolute path (obtained from the `ContentStoreItem.filename` getter) to the `setVoiceByPath` method provided by the `UnlSdkSettings` class:

* Kotlin
* Java

```kotlin
// Kotlin
val filePath = contentStoreItem.filename
UnlSdkSettings.setVoiceByPath(filePath)

```

```java
// Java
String filePath = contentStoreItem.getFilename();
UnlSdkSettings.setVoiceByPath(filePath);

```

> 💡 **TIP**
>
> The `UnlSdkSettings.setVoiceByPath` method can also be used to set computer voices, provided the computer voice path is known.
>
> For setting TTS computer voices, it's recommended to use `UnlSoundPlayingService.setTTSLanguage` with the appropriate language code instead.

### Apply a Voice by language[​](#apply-a-voice-by-language "Direct link to Apply a Voice by language")

Computer voices can be applied using the `setTTSLanguage` method provided by the `UnlSoundPlayingService` class. The method requires a language code string:

* Kotlin
* Java

```kotlin
// Kotlin
// Get available TTS languages
val ttsLanguages = UnlSoundPlayingService.getTTSLanguages()

// Set TTS language by language code
if (ttsLanguages.isNotEmpty()) {
    UnlSoundPlayingService.setTTSLanguage(ttsLanguages[0].code)
}

// Or set directly with a known language code
UnlSoundPlayingService.setTTSLanguage("en-US")

```

```java
// Java
// Get available TTS languages
List<UnlTTSLanguage> ttsLanguages = UnlSoundPlayingService.getTTSLanguages();

// Set TTS language by language code
if (!ttsLanguages.isEmpty()) {
    UnlSoundPlayingService.setTTSLanguage(ttsLanguages.get(0).getCode());
}

// Or set directly with a known language code
UnlSoundPlayingService.setTTSLanguage("en-US");

```

The computer voice is implemented using the device included TTS capabilities.

> 🚨 **DANGER**
>
> Selecting a computer voice in an unsupported language may cause a mismatch between the spoken voice and the instruction content. The exact behavior depends on the device and its available text-to-speech capabilities.

## Get the TTS Instruction Strings[​](#get-the-tts-instruction-strings "Direct link to Get the TTS Instruction Strings")

The TTS instructions can be provided by the `UnlNavigationService` as strings in order to be further processed and played with external tools. For example, Android's built-in TTS engine or third-party TTS libraries can be used for playing sound based on the TTS text instructions provided by the SDK. The voice instructions strings will come on the `onNavigationSound` callback set during navigation or simulation.

Add Android's built-in TTS or a third-party TTS library to your project. For Android's built-in TTS, ensure you have the necessary permissions in your `AndroidManifest.xml`.

We can use the `onNavigationSound` callback of the navigation listener in the following way:

* Kotlin
* Java

```kotlin
// Kotlin
import android.speech.tts.TextToSpeech
import com.unlmap.sdk.core.ISound
import com.unlmap.sdk.routesandnavigation.UnlNavigationInstruction
import com.unlmap.sdk.routesandnavigation.UnlNavigationListener

// instantiate Android TTS
val tts = TextToSpeech(context) { status ->
    if (status == TextToSpeech.SUCCESS) {
        // TTS engine initialized successfully
    }
}

fun simulationInstructionUpdated(instruction: UnlNavigationInstruction) {
    // handle instruction
}

fun handleNavigationSound(sound: ISound) {
    val instructionText = sound.getAsString()
    if (!instructionText.isNullOrEmpty()) {
        tts.speak(instructionText, TextToSpeech.QUEUE_FLUSH, null, "NavigationTTS")
    }
}

val navigationListener = UnlNavigationListener.create(
    onNavigationInstructionUpdated = { instruction ->
        simulationInstructionUpdated(instruction)
    },
    onNavigationSound = { sound ->
        handleNavigationSound(sound)
    }
)

val navigationService = UnlNavigationService()
navigationService.startSimulationWithRoute(
    route = route,
    navigationListener = navigationListener,
    progressListener = UnlProgressListener.create(),
    speedMultiplier = 2.0f
)

```

```java
// Java
import android.speech.tts.TextToSpeech;
import com.unlmap.sdk.core.ISound;
import com.unlmap.sdk.routesandnavigation.UnlNavigationInstruction;
import com.unlmap.sdk.routesandnavigation.UnlNavigationListener;

// instantiate Android TTS
TextToSpeech tts = new TextToSpeech(context, (status) -> {
    if (status == TextToSpeech.SUCCESS) {
        // TTS engine initialized successfully
    }
});

private void simulationInstructionUpdated(UnlNavigationInstruction instruction) {
    // handle instruction
}

private void handleNavigationSound(ISound sound) {
    String instructionText = sound.getAsString();
    if (instructionText != null && !instructionText.isEmpty()) {
        tts.speak(instructionText, TextToSpeech.QUEUE_FLUSH, null, "NavigationTTS");
    }
}

UnlNavigationListener navigationListener = UnlNavigationListener.create(
    /* onNavigationInstructionUpdated */ (instruction) -> {
        simulationInstructionUpdated(instruction);
    },
    /* onNavigationSound */ (sound) -> {
        handleNavigationSound(sound);
    }
);

UnlNavigationService navigationService = new UnlNavigationService();
navigationService.startSimulationWithRoute(
    route,
    navigationListener,
    UnlProgressListener.create(),
    2.0f // speedMultiplier
);

```

For navigation we can set the `onNavigationSound` callback in a similar way with `startNavigation`.

See the Android documentation for [TextToSpeech](https://developer.android.com/reference/android/speech/tts/TextToSpeech) for information on how to set the TTS voice, language and other options such as pitch, volume, speech rate, etc.

> 💡 **TIP**
>
> To change the language of the instructions provided through the `onNavigationSound` callback, you can:
>
>* Use `UnlSoundPlayingService.setTTSLanguage` and specify the preferred language code.
>* Or use `UnlSdkSettings.setVoiceByPath` with a voice path corresponding to the desired language.
>
> To disable the internal playback engine, simply don't call `UnlSoundPlayingService.play()` in your `onNavigationSound` callback. The instruction will still be delivered via the callback, but no audio will be played.

## Monitor UnlSound Playback Events[​](#monitor-sound-playback-events "Direct link to Monitor UnlSound Playback Events")

The Navigation SDK provides sound playback monitoring through the `UnlSoundPlayingListener` interface. This listener receives callbacks during sound operations and allows you to track playback progress.

Here's how to create a custom sound playing listener:

* Kotlin
* Java

```kotlin
// Kotlin
import com.unlmap.sdk.core.UnlSoundPlayingListener
import com.unlmap.sdk.core.UnlSoundPlayingService
import com.unlmap.sdk.core.UnlSoundPlayingPreferences

class CustomSoundPlayingListener : UnlSoundPlayingListener() {
    override fun notifyStart(hasProgress: Boolean) {
        // UnlSound playback started
        println("UnlSound playback started. Has progress: $hasProgress")
    }

    override fun notifyProgress(progress: Int) {
        // Progress update (0-100)
        println("Playback progress: $progress%")
    }

    override fun notifyComplete(errorCode: Int, hint: String) {
        // UnlSound playback completed
        if (errorCode == 0) {
            println("UnlSound playback completed successfully")
        } else {
            println("UnlSound playback failed with error: $errorCode")
        }
    }

    override fun onVolumeChangedByKeys(newVolume: Int) {
        // Volume changed via hardware keys
        println("Volume changed to: $newVolume")
    }
}

// Use the listener when playing sounds
val listener = CustomSoundPlayingListener()
val preferences = UnlSoundPlayingPreferences()

// Play text with monitoring
UnlSoundPlayingService.playText(
    text = "Turn left in 100 meters",
    listener = listener,
    preferences = preferences
)

```

```java
// Java
import com.unlmap.sdk.core.UnlSoundPlayingListener;
import com.unlmap.sdk.core.UnlSoundPlayingService;
import com.unlmap.sdk.core.UnlSoundPlayingPreferences;

public class CustomSoundPlayingListener extends UnlSoundPlayingListener {
    @Override
    public void notifyStart(boolean hasProgress) {
        // UnlSound playback started
        System.out.println("UnlSound playback started. Has progress: " + hasProgress);
    }

    @Override
    public void notifyProgress(int progress) {
        // Progress update (0-100)
        System.out.println("Playback progress: " + progress + "%");
    }

    @Override
    public void notifyComplete(int errorCode, String hint) {
        // UnlSound playback completed
        if (errorCode == 0) {
            System.out.println("UnlSound playback completed successfully");
        } else {
            System.out.println("UnlSound playback failed with error: " + errorCode);
        }
    }

    @Override
    public void onVolumeChangedByKeys(int newVolume) {
        // Volume changed via hardware keys
        System.out.println("Volume changed to: " + newVolume);
    }
}

// Use the listener when playing sounds
CustomSoundPlayingListener listener = new CustomSoundPlayingListener();
UnlSoundPlayingPreferences preferences = new UnlSoundPlayingPreferences();

// Play text with monitoring
UnlSoundPlayingService.playText(
    "Turn left in 100 meters",
    listener,
    preferences
);

```

### Monitoring Navigation Sounds[​](#monitoring-navigation-sounds "Direct link to Monitoring Navigation Sounds")

To monitor navigation sounds specifically, you can create a listener and use it with the sound received from `onNavigationSound`:

* Kotlin
* Java

```kotlin
// Kotlin
val soundListener = object : UnlSoundPlayingListener() {
    override fun notifyStart(hasProgress: Boolean) {
        // Navigation sound started playing
    }

    override fun notifyComplete(errorCode: Int, hint: String) {
        // Navigation sound finished
    }
}

val navigationListener = UnlNavigationListener.create(
    onNavigationSound = { sound ->
        // Play the navigation sound with monitoring
        val preferences = UnlSoundPlayingService.getPlayingPreferences()
        UnlSoundPlayingService.play(sound, soundListener, preferences)
    }
)

```

```java
// Java
UnlSoundPlayingListener soundListener = new UnlSoundPlayingListener() {
    @Override
    public void notifyStart(boolean hasProgress) {
        // Navigation sound started playing
    }

    @Override
    public void notifyComplete(int errorCode, String hint) {
        // Navigation sound finished
    }
};

UnlNavigationListener navigationListener = UnlNavigationListener.create(
    /* onNavigationSound */ (sound) -> {
        // Play the navigation sound with monitoring
        UnlSoundPlayingPreferences preferences = UnlSoundPlayingService.getPlayingPreferences();
        UnlSoundPlayingService.play(sound, soundListener, preferences);
    }
);

```

### Getting Playback Information[​](#getting-playback-information "Direct link to Getting Playback Information")

You can check the current playback status using these methods:

* Kotlin
* Java

```kotlin
// Kotlin
// Check how many sounds are currently playing
val playingCount = UnlSoundPlayingService.playingSoundsCount()

// Get current playing preferences
val preferences = UnlSoundPlayingService.getPlayingPreferences()
val currentVolume = preferences.volume // Volume level (0-10)
val maxPlayingTime = preferences.maxPlayingTime // Max duration in seconds

// Cancel a specific playback
UnlSoundPlayingService.cancel(listener)

```

```java
// Java
// Check how many sounds are currently playing
int playingCount = UnlSoundPlayingService.playingSoundsCount();

// Get current playing preferences
UnlSoundPlayingPreferences preferences = UnlSoundPlayingService.getPlayingPreferences();
int currentVolume = preferences.getVolume(); // Volume level (0-10)
int maxPlayingTime = preferences.getMaxPlayingTime(); // Max duration in seconds

// Cancel a specific playback
UnlSoundPlayingService.cancel(listener);

```

> 📝 **INFO**
>
> The `UnlSoundPlayingListener` is used for all sound operations in the SDK, including TTS, file playback, and navigation sounds. Only one sound can be played at a time through the service.

## Play custom instructions[​](#play-custom-instructions "Direct link to Play custom instructions")

Sometimes you may need to play custom instructions, such as road information warnings or social reports.

To do this, use the `playText` method of the `UnlSoundPlayingService` to play back a custom string. This uses the currently selected TTS voice and requires a listener and preferences.

* Kotlin
* Java

```kotlin
// Kotlin
import com.unlmap.sdk.core.UnlSoundPlayingService
import com.unlmap.sdk.core.UnlSoundPlayingListener
import com.unlmap.sdk.core.UnlSoundPlayingPreferences

// Create a listener for the custom instruction
val customInstructionListener = object : UnlSoundPlayingListener() {
    override fun notifyStart(hasProgress: Boolean) {
        println("Custom instruction started")
    }

    override fun notifyComplete(errorCode: Int, hint: String) {
        if (errorCode == 0) {
            println("Custom instruction completed")
        } else {
            println("Custom instruction failed: $errorCode")
        }
    }
}

// Get default preferences or create custom ones
val preferences = UnlSoundPlayingService.getPlayingPreferences()

// Play a custom instruction
UnlSoundPlayingService.playText(
    text = "Speed camera ahead",
    listener = customInstructionListener,
    preferences = preferences
)

```

```java
// Java
import com.unlmap.sdk.core.UnlSoundPlayingService;
import com.unlmap.sdk.core.UnlSoundPlayingListener;
import com.unlmap.sdk.core.UnlSoundPlayingPreferences;

// Create a listener for the custom instruction
UnlSoundPlayingListener customInstructionListener = new UnlSoundPlayingListener() {
    @Override
    public void notifyStart(boolean hasProgress) {
        System.out.println("Custom instruction started");
    }

    @Override
    public void notifyComplete(int errorCode, String hint) {
        if (errorCode == 0) {
            System.out.println("Custom instruction completed");
        } else {
            System.out.println("Custom instruction failed: " + errorCode);
        }
    }
};

// Get default preferences or create custom ones
UnlSoundPlayingPreferences preferences = UnlSoundPlayingService.getPlayingPreferences();

// Play a custom instruction
UnlSoundPlayingService.playText(
    "Speed camera ahead",
    customInstructionListener,
    preferences
);

```

> 💡 **TIP**
>
> Check the [speed warnings](/01-Overview/04-Todo.md) and [landmark & overlay alarms](/01-Overview/04-Todo.md) docs to learn how to get notified about speed warnings and reports.
