# Navigation instructions

The Navigation SDK for Android offers comprehensive real-time navigation guidance, providing detailed information on the current and upcoming route, including road details, street names, speed limits, and turn directions. It delivers essential data such as remaining travel time, distance to destination, and upcoming turn or road information, ensuring users receive accurate, timely instructions. Designed for both navigation and simulation scenarios, this feature enhances the overall user experience by supporting smooth and efficient route planning and execution.

The main class responsible for turn-by-turn live navigation guidance is the `UnlNavigationInstruction` class.

>  📝 **Info**
>
> It is important to distinguish between `UnlNavigationInstruction` and `UnlRouteInstruction`. `UnlNavigationInstruction` offers real-time, turn-by-turn navigation based on the user's current position and is relevant only during navigation or simulation. In contrast, `UnlRouteInstruction` provides an overview of the entire route available as soon as the route is calculated and the list of instructions do not change as the user navigates on the route.

## Instantiating navigation instructions[​](#instantiating-navigation-instructions "Direct link to Instantiating navigation instructions")

Navigation instructions cannot be directly instantiated. Instead, they must be provided by the SDK while navigating. For detailed guidance on how to navigate on routes, refer to the [Getting Started with Navigation Guide](/docs/android/guides/navigation/get-started-navigation.md).

There are two main ways of getting a navigation instruction:

* `UnlNavigationInstruction` instances can be obtained via the callback provided as parameters to the `startNavigation` and `startSimulation` methods through the `UnlNavigationListener`.
* The `UnlNavigationService` class provides a `getNavigationInstruction` method which returns the currently available navigation instruction. Make sure navigation/simulation is active before using the method provided above.

### Import statements[​](#import-statements "Direct link to Import statements")

To work with navigation instructions in your Android project, you'll need the following imports:

* Kotlin
* Java

```kotlin
// Kotlin
import com.unlmap.sdk.routesandnavigation.UnlNavigationInstruction
import com.unlmap.sdk.routesandnavigation.UnlNavigationListener
import com.unlmap.sdk.routesandnavigation.UnlNavigationService
import com.unlmap.sdk.core.Rgba
import com.unlmap.sdk.util.GemUtilImages
import com.unlmap.sdk.util.SdkCall
import android.graphics.Bitmap
import android.widget.Toast

```

```java
//Java
import com.unlmap.sdk.routesandnavigation.UnlNavigationInstruction;
import com.unlmap.sdk.routesandnavigation.UnlNavigationListener;
import com.unlmap.sdk.routesandnavigation.UnlNavigationService;
import com.unlmap.sdk.core.Rgba;
import com.unlmap.sdk.util.GemUtilImages;
import com.unlmap.sdk.util.SdkCall;
import android.graphics.Bitmap;
import android.widget.Toast;

```

### Getting navigation instructions through UnlNavigationListener[​](#getting-navigation-instructions-through-navigationlistener "Direct link to Getting navigation instructions through UnlNavigationListener")

The most common way to receive navigation instructions is through the `UnlNavigationListener.onNavigationInstructionUpdated` callback:

* Kotlin
* Java

```kotlin
// Kotlin
private val navigationListener: UnlNavigationListener = UnlNavigationListener.create(
    onNavigationInstructionUpdated = { instr ->
        // Handle the navigation instruction
        val currentStreetName = instr.currentStreetName
        val nextTurnInstruction = instr.nextTurnInstruction

        // Process instruction data...
    }
)

```

```java
// Java
private final UnlNavigationListener navigationListener = UnlNavigationListener.create(
    instr -> {
        // Handle the navigation instruction
        String currentStreetName = instr.getCurrentStreetName();
        String nextTurnInstruction = instr.getNextTurnInstruction();

        // Process instruction data...
    }
);

```

### Getting navigation instructions from UnlNavigationService[​](#getting-navigation-instructions-from-navigationservice "Direct link to Getting navigation instructions from UnlNavigationService")

You can also directly query the navigation service for the current instruction:

* Kotlin
* Java

```kotlin
// Kotlin
private val navigationService = UnlNavigationService()

// Get current navigation instruction (ensure navigation is active)
val currentInstruction = navigationService.getNavigationInstruction()

```

```java
// Java
private final UnlNavigationService navigationService = new UnlNavigationService();

// Get current navigation instruction (ensure navigation is active)
UnlNavigationInstruction currentInstruction = navigationService.getNavigationInstruction();

```

## UnlNavigationInstruction structure[​](#navigationinstruction-structure "Direct link to UnlNavigationInstruction structure")

| Member                                      | Type                    | Description                                                                                                                          |
| ------------------------------------------- | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `currentCountryCodeISO`                     | String?                 | Returns the ISO 3166-1 alpha-3 country code for the current navigation instruction. Empty string means no country.                   |
| `currentStreetName`                         | String?                 | Returns the current street name.                                                                                                     |
| `currentStreetSpeedLimit`                   | Double                  | Returns the maximum speed limit on the current street in meters per second. Returns 0 if not available.                              |
| `driveSide`                                 | `EUnlDriveSide`            | Returns the drive side flag of the current traveled road.                                                                            |
| `hasNextNextTurnInfo()`                     | Boolean                 | Returns true if next-next turn information is available.                                                                             |
| `hasNextTurnInfo()`                         | Boolean                 | Returns true if next turn information is available.                                                                                  |
| `instructionIndex`                          | Int                     | Returns the index of the current route instruction on the current route segment.                                                     |
| `laneImage`                                 | `UnlImage?`                | Returns a customizable image representation of current lane configuration. The user is responsible to verify if the image is valid.  |
| `navigationStatus`                          | `EUnlNavigationStatus`     | Returns the navigation/simulation status.                                                                                            |
| `nextCountryCodeISO`                        | String?                 | Returns the ISO 3166-1 alpha-3 country code for the next navigation instruction.                                                     |
| `nextNextStreetName`                        | String?                 | Returns the next-next street name.                                                                                                   |
| `nextNextTurnDetails`                       | `UnlTurnDetails?`          | Returns the full details for the next-next turn. Used for customizing turn display in UI.                                            |
| `nextNextTurnInstruction`                   | String?                 | Returns the textual description for the next-next turn.                                                                              |
| `nextStreetName`                            | String?                 | Returns the next street name.                                                                                                        |
| `nextTurnDetails`                           | `UnlTurnDetails?`          | Returns the full details for the next turn. Used for customizing turn display in UI.                                                 |
| `nextTurnInstruction`                       | String?                 | Returns the textual description for the next turn.                                                                                   |
| `remainingTravelTimeDistance`               | `UnlTimeDistance?`         | Returns the remaining travel time in seconds and distance in meters.                                                                 |
| `remainingTravelTimeDistanceToNextWaypoint` | `UnlTimeDistance?`         | Returns the remaining travel time in seconds and distance in meters to the next waypoint.                                            |
| `currentRoadInformation`                    | `List&lt;RoadInfo&gt;?` | Returns the current road information list.                                                                                           |
| `nextRoadInformation`                       | `List&lt;RoadInfo&gt;?` | Returns the next road information list.                                                                                              |
| `nextNextRoadInformation`                   | `List&lt;RoadInfo&gt;?` | Returns the next-next road information list.                                                                                         |
| `segmentIndex`                              | Int                     | Returns the index of the current route segment.                                                                                      |
| `signpostDetails`                           | SignpostDetails?        | Returns the extended signpost details.                                                                                               |
| `signpostInstruction`                       | String?                 | Returns the textual description for the signpost information.                                                                        |
| `timeDistanceToNextNextTurn`                | `UnlTimeDistance?`         | Returns the time (seconds) and distance (meters) to the next-next turn. Returns values for next turn if no next-next turn available. |
| `timeDistanceToNextTurn`                    | `UnlTimeDistance?`         | Returns the time (seconds) and distance (meters) to the next turn.                                                                   |
| `traveledTimeDistance`                      | `UnlTimeDistance?`         | Returns the traveled time in seconds and distance in meters.                                                                         |

The field `nextTurnInstruction` provides an instruction in text format, suitable for displaying on UI. Please use the `onNavigationSound` callback in UnlNavigationListener for getting an instruction suitable for text-to-speech.

## Turn details[​](#turn-details "Direct link to Turn details")

### Next turn details[​](#next-turn-details "Direct link to Next turn details")

The following snippet shows how to extract detailed instructions for the next turn along the route. It's typically used in the navigation UI to show users the upcoming maneuver. You may also use this to provide turn-by-turn instructions with images or detailed text for navigation display:

* Kotlin
* Java

```kotlin
// Kotlin
// If hasNextTurnInfo() is false some details are not available
val hasNextTurnInfo = navigationInstruction.hasNextTurnInfo()

if (hasNextTurnInfo) {
    // The next turn instruction
    val nextTurnInstruction = navigationInstruction.nextTurnInstruction

    // The next turn details
    val turnDetails = navigationInstruction.nextTurnDetails

    turnDetails?.let {
        // Turn event type (continue straight, turn right, turn left, etc.)
        val event = it.event

        // Roundabout exit number (-1 if not a roundabout)
        val roundaboutExitNumber = it.roundaboutExitNumber

        // Get turn image using GemUtilImages
        val turnImageSize = 300
        val turnImage = getNextTurnImage(navigationInstruction, turnImageSize, turnImageSize)
    }
}

// Helper function to get turn image (based on sample code)
private fun getNextTurnImage(
    navInstr: UnlNavigationInstruction,
    width: Int,
    height: Int
): Bitmap? {
    return SdkCall.execute {
        if (!navInstr.hasNextTurnInfo()) return@execute null

        val image = navInstr.nextTurnDetails?.abstractGeometryImage

        val aInner = Rgba(255, 255, 255, 255)
        val aOuter = Rgba(0, 0, 0, 255)
        val iInner = Rgba(128, 128, 128, 255)
        val iOuter = Rgba(128, 128, 128, 255)

        GemUtilImages.asBitmap(image, width, height, aInner, aOuter, iInner, iOuter)
    }
}

```

```java
// Java
// If hasNextTurnInfo() is false some details are not available
boolean hasNextTurnInfo = navigationInstruction.hasNextTurnInfo();

if (hasNextTurnInfo) {
    // The next turn instruction
    String nextTurnInstruction = navigationInstruction.getNextTurnInstruction();

    // The next turn details
    UnlTurnDetails turnDetails = navigationInstruction.getNextTurnDetails();

    if (turnDetails != null) {
        // Turn event type (continue straight, turn right, turn left, etc.)
        Object event = turnDetails.getEvent();

        // Roundabout exit number (-1 if not a roundabout)
        int roundaboutExitNumber = turnDetails.getRoundaboutExitNumber();

        // Get turn image using GemUtilImages
        int turnImageSize = 300;
        Bitmap turnImage = getNextTurnImage(navigationInstruction, turnImageSize, turnImageSize);
    }
}

// Helper method to get turn image (based on sample code)
private Bitmap getNextTurnImage(
    UnlNavigationInstruction navInstr,
    int width,
    int height
) {
    return SdkCall.execute(() -> {
        if (!navInstr.hasNextTurnInfo()) return null;

        UnlTurnDetails details = navInstr.getNextTurnDetails();
        Object image = details != null ? details.getAbstractGeometryImage() : null;

        Rgba aInner = new Rgba(255, 255, 255, 255);
        Rgba aOuter = new Rgba(0, 0, 0, 255);
        Rgba iInner = new Rgba(128, 128, 128, 255);
        Rgba iOuter = new Rgba(128, 128, 128, 255);

        return GemUtilImages.asBitmap(image, width, height, aInner, aOuter, iInner, iOuter);
    });
}

```

See the [TurnDetails](../03-Core/08-Routes.md#turn-details) guide for more details about the fields within the `UnlTurnDetails` class.

### Next next turn details[​](#next-next-turn-details "Direct link to Next next turn details")

Details about the subsequent turn (i.e., the turn following the next one) can be crucial for certain use cases, such as providing a preview of upcoming maneuvers. These details can be accessed in a similar manner:

* Kotlin
* Java

```kotlin
// Kotlin
// If hasNextNextTurnInfo() is false some details are not available
val hasNextNextTurnInfo = navigationInstruction.hasNextNextTurnInfo()

if (hasNextNextTurnInfo) {
    val nextNextTurnInstruction = navigationInstruction.nextNextTurnInstruction

    val nextNextTurnDetails = navigationInstruction.nextNextTurnDetails

    // Get next next turn image similarly to next turn image
    nextNextTurnDetails?.let {
        val nextNextTurnImage = getNextNextTurnImage(navigationInstruction, 300, 300)
    }
}

// Helper function for next next turn image
private fun getNextNextTurnImage(
    navInstr: UnlNavigationInstruction,
    width: Int,
    height: Int
): Bitmap? {
    return SdkCall.execute {
        if (!navInstr.hasNextNextTurnInfo()) return@execute null

        val image = navInstr.nextNextTurnDetails?.abstractGeometryImage

        val aInner = Rgba(255, 255, 255, 255)
        val aOuter = Rgba(0, 0, 0, 255)
        val iInner = Rgba(128, 128, 128, 255)
        val iOuter = Rgba(128, 128, 128, 255)

        GemUtilImages.asBitmap(image, width, height, aInner, aOuter, iInner, iOuter)
    }
}

```

```java
// Java
// If hasNextNextTurnInfo() is false some details are not available
boolean hasNextNextTurnInfo = navigationInstruction.hasNextNextTurnInfo();

if (hasNextNextTurnInfo) {
    String nextNextTurnInstruction = navigationInstruction.getNextNextTurnInstruction();

    UnlTurnDetails nextNextTurnDetails = navigationInstruction.getNextNextTurnDetails();

    // Get next next turn image similarly to next turn image
    if (nextNextTurnDetails != null) {
        Bitmap nextNextTurnImage = getNextNextTurnImage(navigationInstruction, 300, 300);
    }
}

// Helper method for next next turn image
private Bitmap getNextNextTurnImage(
    UnlNavigationInstruction navInstr,
    int width,
    int height
) {
    return SdkCall.execute(() -> {
        if (!navInstr.hasNextNextTurnInfo()) return null;

        UnlTurnDetails details = navInstr.getNextNextTurnDetails();
        Object image = details != null ? details.getAbstractGeometryImage() : null;

        Rgba aInner = new Rgba(255, 255, 255, 255);
        Rgba aOuter = new Rgba(0, 0, 0, 255);
        Rgba iInner = new Rgba(128, 128, 128, 255);
        Rgba iOuter = new Rgba(128, 128, 128, 255);

        return GemUtilImages.asBitmap(image, width, height, aInner, aOuter, iInner, iOuter);
    });
}

```

The `hasNextNextTurnInfo()` might be false if the next instruction is the destination. The same operations discussed earlier for the next turn details can also be applied to the subsequent turn details.

## Street information[​](#street-information "Direct link to Street information")

### Current street information[​](#current-street-information "Direct link to Current street information")

The following snippet shows how to get information about the current road:

* Kotlin
* Java

```kotlin
// Kotlin
// Current street name
val currentStreetName = navigationInstruction.currentStreetName

// Road info related to the current road
val currentRoadInfo = navigationInstruction.currentRoadInformation

// Country ISO code
val countryCode = navigationInstruction.currentCountryCodeISO

// The drive direction (left or right)
val driveDirection = navigationInstruction.driveSide

```

```java
// Java
// Current street name
String currentStreetName = navigationInstruction.getCurrentStreetName();

// Road info related to the current road
List<RoadInfo> currentRoadInfo = navigationInstruction.getCurrentRoadInformation();

// Country ISO code
String countryCode = navigationInstruction.getCurrentCountryCodeISO();

// The drive direction (left or right)
EUnlDriveSide driveDirection = navigationInstruction.getDriveSide();

```

It is important to note that some streets may not have an assigned name. In such cases, `currentStreetName` will return null. The `RoadInfo` class offers additional details, including the road name and shield type, which correspond to the official codes or names assigned to a road.

For example, the `currentStreetName` might return "Bloomsbury Street," while the `roadname` field in the associated RoadInfo instance could provide the official designation, such as "A400." This distinction ensures comprehensive road identification.

### Next & next next street information[​](#next--next-next-street-information "Direct link to Next & next next street information")

Information about the next street, as well as the street following it (next-next street), can also be retrieved. These details include the street name, type, and other associated metadata, enabling enhanced navigation and situational awareness:

* Kotlin
* Java

```kotlin
// Kotlin
// Street name
val nextStreetName = navigationInstruction.nextStreetName
val nextNextStreetName = navigationInstruction.nextNextStreetName

// Road info
val nextRoadInformation = navigationInstruction.nextRoadInformation
val nextNextRoadInformation = navigationInstruction.nextNextRoadInformation

// Next country iso code
val nextCountryCodeISO = navigationInstruction.nextCountryCodeISO

```

```java
// Java
// Street name
String nextStreetName = navigationInstruction.getNextStreetName();
String nextNextStreetName = navigationInstruction.getNextNextStreetName();

// Road info
List<RoadInfo> nextRoadInformation = navigationInstruction.getNextRoadInformation();
List<RoadInfo> nextNextRoadInformation = navigationInstruction.getNextNextRoadInformation();

// Next country iso code
String nextCountryCodeISO = navigationInstruction.getNextCountryCodeISO();

```

The fields associated with these streets retain the same meanings as discussed earlier about the current street.

> 🚨 **Danger**
>
> Ensure that `hasNextTurnInfo()` and `hasNextNextTurnInfo()` are true before attempting to access the respective fields. This verification prevents errors and ensures the availability of reliable data for the requested information.

## Speed limit information[​](#speed-limit-information "Direct link to Speed limit information")

The `UnlNavigationInstruction` class not only provides information about the current road's speed limit but also offers details about upcoming speed limits within a specified distance, assuming the user adheres to the recommended navigation route.

The following snippet demonstrates how to retrieve these details and handle various scenarios appropriately:

* Kotlin
* Java

```kotlin
// Kotlin
SdkCall.execute {
  // The current street speed limit in m/s (0.0 if not available)
  val currentStreetSpeedLimit = navigationInstruction.currentStreetSpeedLimit
}

```

```java
// Java
SdkCall.execute(() -> {
  // The current street speed limit in m/s (0.0 if not available)
  double currentStreetSpeedLimit = navigationInstruction.getCurrentStreetSpeedLimit();
});

```

## Lane image[​](#lane-image "Direct link to Lane image")

The lane image can be used to more effectively illustrate the correct lane for upcoming turns, providing clearer guidance:

* Kotlin
* Java

```kotlin
// Kotlin
val laneImage = navigationInstruction.laneImage

// Helper function to get lane image (based on sample code)
private fun getLaneInfoImage(
    navInstr: UnlNavigationInstruction,
    width: Int,
    height: Int
): Pair<Int, Bitmap?> {
    return SdkCall.execute {
        var resultWidth = width
        if (resultWidth == 0)
            resultWidth = (2.5 * height).toInt()

        val bkColor = Rgba(118, 99, 200, 255)
        val activeColor = Rgba(255, 255, 255, 255)
        val inactiveColor = Rgba(24, 33, 21, 255)

        val image = navInstr.laneImage

        val resultPair = GemUtilImages.asBitmap(
            image,
            resultWidth,
            height,
            bkColor,
            activeColor,
            inactiveColor
        )

        // resultPair is Pair<Int, Bitmap?> where first is error code, second is bitmap
        val errorCode = resultPair.first
        val bitmap = resultPair.second

        return@execute Pair(errorCode, bitmap)
    } ?: Pair(-1, null)
}

// Usage example
val laneImageData = getLaneInfoImage(navigationInstruction, 500, 300)
val errorCode = laneImageData.first
val laneImageBitmap = laneImageData.second

if (errorCode == 0 && laneImageBitmap != null) {
    // Successfully obtained lane image bitmap
    // Use laneImageBitmap for display
} else {
    // Handle error case
    println("Failed to get lane image, error code: $errorCode")
}

```

```java
// Java
Object laneImage = navigationInstruction.getLaneImage();

// Helper method to get lane image (based on sample code)
private Pair<Integer, Bitmap> getLaneInfoImage(
    UnlNavigationInstruction navInstr,
    int width,
    int height
) {
    Pair<Integer, Bitmap> result = SdkCall.execute(() -> {
        int resultWidth = width;
        if (resultWidth == 0)
            resultWidth = (int) (2.5 * height);

        Rgba bkColor = new Rgba(118, 99, 200, 255);
        Rgba activeColor = new Rgba(255, 255, 255, 255);
        Rgba inactiveColor = new Rgba(24, 33, 21, 255);

        Object image = navInstr.getLaneImage();

        Pair<Integer, Bitmap> resultPair = GemUtilImages.asBitmap(
            image,
            resultWidth,
            height,
            bkColor,
            activeColor,
            inactiveColor
        );

        // resultPair is Pair<Integer, Bitmap> where first is error code, second is bitmap
        int errorCode = resultPair.first;
        Bitmap bitmap = resultPair.second;

        return new Pair<>(errorCode, bitmap);
    });
    if (result != null) {
        return result;
    }
    return new Pair<>(-1, null);
}

// Usage example
Pair<Integer, Bitmap> laneImageData = getLaneInfoImage(navigationInstruction, 500, 300);
int errorCode = laneImageData.first;
Bitmap laneImageBitmap = laneImageData.second;

if (errorCode == 0 && laneImageBitmap != null) {
    // Successfully obtained lane image bitmap
    // Use laneImageBitmap for display
} else {
    // Handle error case
    System.out.println("Failed to get lane image, error code: " + errorCode);
}

```

Below is an example of a rendered lane image:

![](/assets/icons/lane_image.png "Lane image containing three lanes")

**Lane image containing three lanes**

## Change the language of the instructions[​](#change-the-language-of-the-instructions "Direct link to Change the language of the instructions")

The texts used in navigation instructions and related classes follow the language set in the SDK. See [the internationalization guide](/01-Overview/04-Todo.md) for more details.
