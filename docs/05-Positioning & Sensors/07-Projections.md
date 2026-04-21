# Projections

Besides the `UnlCoordinates` class, the Navigation SDK for Android provides a `Projection` abstract class that represents the base class for different geocoordinate systems such as:

* `ProjectionWGS84` (World Geodetic System 1984)
* `ProjectionGK` (Gauss-Kruger)
* `ProjectionUTM` (Universal Transverse Mercator)
* `ProjectionLAM` (Lambert)
* `ProjectionBNG` (British National Grid)
* `ProjectionMGRS` (Military Grid Reference System)

To work with projections, you'll need to import the following classes:

* Kotlin
* Java

```kotlin
// Kotlin
import com.unlmap.sdk.places.UnlCoordinates
import com.unlmap.sdk.projection.*
import com.unlmap.sdk.core.UnlProgressListener
import com.unlmap.sdk.core.UnlError

```

```java
// Java
import com.unlmap.sdk.places.UnlCoordinates;
import com.unlmap.sdk.projection.*;
import com.unlmap.sdk.core.UnlProgressListener;
import com.unlmap.sdk.core.UnlError;

```

To know the type of the `Projection` you can use the `type` property:

* Kotlin
* Java

```kotlin
// Kotlin
val type = projection.type

```

```java
// Java
EProjectionType type = projection.getType();

```

## ProjectionWGS84[​](#projectionwgs84 "Direct link to ProjectionWGS84")

The `ProjectionWGS84` projection is a widely used geodetic datum that serves as the foundation for GPS and other mapping systems. It provides a standard reference frame for the Earth's surface, allowing for accurate positioning and navigation. A `ProjectionWGS84` projection can be instantiated using a `UnlCoordinates` object:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionWGS84(UnlCoordinates(latitude = 5.0, longitude = 5.0))

```

```java
// Java
ProjectionWGS84 obj = new ProjectionWGS84(new UnlCoordinates(5.0, 5.0));

```

Then, the coordinates can be accessed and set using the `coordinates` property:

* Kotlin
* Java

```kotlin
// Kotlin
val coordinates = obj.coordinates // Get coordinates
obj.coordinates = UnlCoordinates(latitude = 10.0, longitude = 10.0) // Set coordinates

```

```java
// Java
UnlCoordinates coordinates = obj.getCoordinates(); // Get coordinates
obj.setCoordinates(new UnlCoordinates(10.0, 10.0)); // Set coordinates

```

## ProjectionGK[​](#projectiongk "Direct link to ProjectionGK")

The `ProjectionGK` (Gauss-Kruger) projection is a cylindrical map projection that is commonly used for large-scale mapping in regions with a north-south orientation. It divides the Earth into zones, each with its own coordinate system, allowing for accurate representation of geographic features. A `ProjectionGK` projection can be instantiated using the following constructor:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionGK(x = 6325113.72, y = 5082540.66, zone = 1)

```

```java
// Java
ProjectionGK obj = new ProjectionGK(6325113.72, 5082540.66, 1);

```

In order to obtain the x, y and zone values, the `getEasting()`, `getNorthing()` and `getZone()` methods can be used, while setting them can be done using the `set()` method:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionGK(x = 6325113.72, y = 5082540.66, zone = 1)

val type = obj.type // EProjectionType.EPR_Gk
val zone = obj.getZone() // 1
val easting = obj.getEasting() // 6325113.72
val northing = obj.getNorthing() // 5082540.66

obj.set(x = 1.0, y = 1.0, zone = 2)

val newZone = obj.getZone() // 2
val newEasting = obj.getEasting() // 1.0
val newNorthing = obj.getNorthing() // 1.0

```

```java
// Java
ProjectionGK obj = new ProjectionGK(6325113.72, 5082540.66, 1);

EProjectionType type = obj.getType(); // EProjectionType.EPR_Gk
int zone = obj.getZone(); // 1
double easting = obj.getEasting(); // 6325113.72
double northing = obj.getNorthing(); // 5082540.66

obj.set(1.0, 1.0, 2);

int newZone = obj.getZone(); // 2
double newEasting = obj.getEasting(); // 1.0
double newNorthing = obj.getNorthing(); // 1.0

```

> 🚨 **DANGER**
>
> The `ProjectionGK` projection is currently supported only for countries that use **Bessel ellipsoid**. Trying to convert to and from `ProjectionGK` projection for other countries will result in a `UnlError.notSupported` error.

## ProjectionBNG[​](#projectionbng "Direct link to ProjectionBNG")

The `ProjectionBNG` (British National Grid) projection is a coordinate system used in Great Britain for mapping and navigation. It provides a grid reference system that allows for precise location identification within the country. A `ProjectionBNG` projection can be instantiated using the following constructor:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionBNG(easting = 500000.0, northing = 4649776.0)

```

```java
// Java
ProjectionBNG obj = new ProjectionBNG(500000.0, 4649776.0);

```

Alternatively, you can create a `ProjectionBNG` using a grid reference string:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionBNG("TQ123456")

```

```java
// Java
ProjectionBNG obj = new ProjectionBNG("TQ123456");

```

In order to obtain the easting and northing values, the `getEasting()` and `getNorthing()` methods can be used, while setting them can be done using the `set()` method. You can also access the grid reference using the `gridReference` property:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionBNG(easting = 6325113.72, northing = 5082540.66)

obj.set(easting = 1.0, northing = 1.0)

val type = obj.type // EProjectionType.EPR_Bng
val newEasting = obj.getEasting() // 1.0
val newNorthing = obj.getNorthing() // 1.0
val gridRef = obj.gridReference // Grid reference string

```

```java
// Java
ProjectionBNG obj = new ProjectionBNG(6325113.72, 5082540.66);

obj.set(1.0, 1.0);

EProjectionType type = obj.getType(); // EProjectionType.EPR_Bng
double newEasting = obj.getEasting(); // 1.0
double newNorthing = obj.getNorthing(); // 1.0
String gridRef = obj.getGridReference(); // Grid reference string

```

## ProjectionMGRS[​](#projectionmgrs "Direct link to ProjectionMGRS")

The `ProjectionMGRS` (Military Grid Reference System) projection is a coordinate system used by the military for precise location identification. It combines the UTM and UPS coordinate systems to provide a grid reference system that is easy to use in the field. A `ProjectionMGRS` projection can be instantiated using the following constructor:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionMGRS(easting = 99316, northing = 10163, zone = "30U", letters = "XC")

```

```java
// Java
ProjectionMGRS obj = new ProjectionMGRS(99316, 10163, "30U", "XC");

```

In order to obtain the easting, northing, zone and letters values, the `getEasting()`, `getNorthing()`, `getZone()` and `getSq100kIdentifier()` methods can be used, while setting them can be done using the `set()` method:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionMGRS(
    easting = 6325113, northing = 5082540, zone = "A", letters = "letters")

obj.set(
    easting = 1, northing = 1, zone = "B", letters = "newLetters")

val type = obj.type // EProjectionType.EPR_Mgrs
val newZone = obj.getZone() // "B"
val newEasting = obj.getEasting() // 1
val newNorthing = obj.getNorthing() // 1
val newLetters = obj.getSq100kIdentifier() // "newLetters"

```

```java
// Java
ProjectionMGRS obj = new ProjectionMGRS(
    6325113, 5082540, "A", "letters");

obj.set(
    1, 1, "B", "newLetters");

EProjectionType type = obj.getType(); // EProjectionType.EPR_Mgrs
String newZone = obj.getZone(); // "B"
int newEasting = obj.getEasting(); // 1
int newNorthing = obj.getNorthing(); // 1
String newLetters = obj.getSq100kIdentifier(); // "newLetters"

```


## ProjectionLAM[​](#projectionlam "Direct link to ProjectionLAM")

The `ProjectionLAM` (Lambert) projection is a conic map projection that is commonly used for large-scale mapping in regions with an east-west orientation. It provides a way to represent geographic features accurately while minimizing distortion. A `ProjectionLAM` projection can be instantiated using the following constructor:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionLAM(x = 6325113.72, y = 5082540.66)

```

```java
// Java
ProjectionLAM obj = new ProjectionLAM(6325113.72, 5082540.66);

```

In order to obtain the x and y values, the `getX()` and `getY()` methods can be used, while setting them can be done using the `set()` method:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionLAM(x = 6325113.72, y = 5082540.66)

obj.set(x = 1.0, y = 1.0)

val type = obj.type // EProjectionType.EPR_Lam
val newX = obj.getX() // 1.0
val newY = obj.getY() // 1.0

```

```java
// Java
ProjectionLAM obj = new ProjectionLAM(6325113.72, 5082540.66);

obj.set(1.0, 1.0);

EProjectionType type = obj.getType(); // EProjectionType.EPR_Lam
double newX = obj.getX(); // 1.0
double newY = obj.getY(); // 1.0

```

## ProjectionUTM[​](#projectionutm "Direct link to ProjectionUTM")

The `ProjectionUTM` (Universal Transverse Mercator) projection is a global map projection that divides the world into a series of zones, each with its own coordinate system. It provides a way to represent geographic features accurately while minimizing distortion. A `ProjectionUTM` projection can be instantiated using the following constructor:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionUTM(x = 6325113.72, y = 5082540.66, zone = 1, hemisphere = EHemisphere.HEM_South)

```

```java
// Java
ProjectionUTM obj = new ProjectionUTM(6325113.72, 5082540.66, 1, EHemisphere.HEM_South);

```

In order to obtain the x, y, zone and hemisphere values, the `getX()`, `getY()`, `getZone()` and `getHemisphere()` methods can be used, while setting them can be done using the `set()` method:

* Kotlin
* Java

```kotlin
// Kotlin
val obj = ProjectionUTM(x = 6325113.72, y = 5082540.66, zone = 1, hemisphere = EHemisphere.HEM_South)

obj.set(x = 1.0, y = 1.0, zone = 2, hemisphere = EHemisphere.HEM_North.value)

val type = obj.type // EProjectionType.EPR_Utm
val newZone = obj.getZone() // 2
val newX = obj.getX() // 1.0
val newY = obj.getY() // 1.0
val newHemisphere = EHemisphere.entries[obj.getHemisphere()] // EHemisphere.HEM_North

```

```java
// Java
ProjectionUTM obj = new ProjectionUTM(6325113.72, 5082540.66, 1, EHemisphere.HEM_South);

obj.set(1.0, 1.0, 2, EHemisphere.HEM_North.getValue());

EProjectionType type = obj.getType(); // EProjectionType.EPR_Utm
int newZone = obj.getZone(); // 2
double newX = obj.getX(); // 1.0
double newY = obj.getY(); // 1.0
EHemisphere newHemisphere = EHemisphere.values()[obj.getHemisphere()]; // EHemisphere.HEM_North

```

## Projection Service[​](#projection-service "Direct link to Projection Service")

The `ProjectionService` object provides a method to convert between different projection types. It allows you to transform coordinates from one projection to another, making it easier to work with various geospatial data formats. The class is an object and features a static `convert` method:

* Kotlin
* Java

```kotlin
// Kotlin
val from = ProjectionWGS84(UnlCoordinates(latitude = 51.5074, longitude = -0.1278))
val to = ProjectionMGRS()

val progressListener = UnlProgressListener.create(
    onCompleted = { errorCode, _ ->
        if (errorCode == UnlError.NoError) {
            // Conversion successful
            val easting = to.getEasting() // 99316
            val northing = to.getNorthing() // 10163
            val zone = to.getZone() // "30U"
            val letters = to.getSq100kIdentifier() // "XC"
        } else {
            // Handle conversion error
            println("Conversion failed with error: $errorCode")
        }
    }
)

// Start the conversion
ProjectionService.convert(from, to, progressListener)

```

```java
// Java
ProjectionWGS84 from = new ProjectionWGS84(new UnlCoordinates(51.5074, -0.1278));
ProjectionMGRS to = new ProjectionMGRS();

UnlProgressListener progressListener = UnlProgressListener.create(
    (errorCode, hint) -> {
        if (errorCode == UnlError.NoError) {
            // Conversion successful
            int easting = to.getEasting(); // 99316
            int northing = to.getNorthing(); // 10163
            String zone = to.getZone(); // "30U"
            String letters = to.getSq100kIdentifier(); // "XC"
        } else {
            // Handle conversion error
            System.out.println("Conversion failed with error: " + errorCode);
        }
    }
);

// Start the conversion
ProjectionService.convert(from, to, progressListener);

```


## Practical Example[​](#practical-example "Direct link to Practical Example")

Here's a practical example showing how to convert a WGS84 coordinate to multiple projection systems, similar to the approach used in the SDK's sample applications:

* Kotlin
* Java

```kotlin
// Kotlin
// Starting coordinates
val wgs84Projection = ProjectionWGS84(UnlCoordinates(latitude = 51.5074, longitude = -0.1278))

// Convert to different projection types
val projectionTypes = listOf(
    EProjectionType.EPR_Mgrs,
    EProjectionType.EPR_Utm,
    EProjectionType.EPR_Bng,
    EProjectionType.EPR_Lam,
    EProjectionType.EPR_Gk
)

for (projectionType in projectionTypes) {
    val targetProjection: Projection = when (projectionType) {
        EProjectionType.EPR_Mgrs -> ProjectionMGRS()
        EProjectionType.EPR_Utm -> ProjectionUTM()
        EProjectionType.EPR_Bng -> ProjectionBNG()
        EProjectionType.EPR_Lam -> ProjectionLAM()
        EProjectionType.EPR_Gk -> ProjectionGK()
        EProjectionType.
            }
        }
        else -> continue
    }

    val progressListener = UnlProgressListener.create(
        onCompleted = { errorCode, _ ->
            if (errorCode == UnlError.NoError) {
                // Handle successful conversion
                when (targetProjection) {
                    is ProjectionMGRS -> {
                        println("MGRS: ${targetProjection.getEasting()}, ${targetProjection.getNorthing()}")
                        println("Zone: ${targetProjection.getZone()}, Letters: ${targetProjection.getSq100kIdentifier()}")
                    }
                    is ProjectionUTM -> {
                        println("UTM: ${targetProjection.getX()}, ${targetProjection.getY()}")
                        println("Zone: ${targetProjection.getZone()}, Hemisphere: ${EHemisphere.entries[targetProjection.getHemisphere()]}")
                    }
                    is ProjectionBNG -> {
                        println("BNG: ${targetProjection.getEasting()}, ${targetProjection.getNorthing()}")
                        println("Grid Reference: ${targetProjection.gridReference}")
                    }
                    // Add other projection types as needed
                }
            } else {
                println("Conversion to ${projectionType} failed with error: $errorCode")
            }
        }
    )

    ProjectionService.convert(wgs84Projection, targetProjection, progressListener)
}

```

```java
// Java
// Starting coordinates
ProjectionWGS84 wgs84Projection = new ProjectionWGS84(new UnlCoordinates(51.5074, -0.1278));

// Convert to different projection types
EProjectionType[] projectionTypes = {
    EProjectionType.EPR_Mgrs,
    EProjectionType.EPR_Utm,
    EProjectionType.EPR_Bng,
    EProjectionType.EPR_Lam,
    EProjectionType.EPR_Gk
};

for (EProjectionType projectionType : projectionTypes) {
    Projection targetProjection;
    switch (projectionType) {
        case EPR_Mgrs:
            targetProjection = new ProjectionMGRS();
            break;
        case EPR_Utm:
            targetProjection = new ProjectionUTM();
            break;
        case EPR_Bng:
            targetProjection = new ProjectionBNG();
            break;
        case EPR_Lam:
            targetProjection = new ProjectionLAM();
            break;
        case EPR_Gk:
            targetProjection = new ProjectionGK();
            break;
        default:
            continue;
    }

    Projection finalTarget = targetProjection;
    UnlProgressListener progressListener = UnlProgressListener.create(
        (errorCode, hint) -> {
            if (errorCode == UnlError.NoError) {
                // Handle successful conversion
                if (finalTarget instanceof ProjectionMGRS) {
                    ProjectionMGRS mgrs = (ProjectionMGRS) finalTarget;
                    System.out.println("MGRS: " + mgrs.getEasting() + ", " + mgrs.getNorthing());
                    System.out.println("Zone: " + mgrs.getZone() + ", Letters: " + mgrs.getSq100kIdentifier());
                } else if (finalTarget instanceof ProjectionUTM) {
                    ProjectionUTM utm = (ProjectionUTM) finalTarget;
                    System.out.println("UTM: " + utm.getX() + ", " + utm.getY());
                    System.out.println("Zone: " + utm.getZone() + ", Hemisphere: " + EHemisphere.values()[utm.getHemisphere()]);
                } else if (finalTarget instanceof ProjectionBNG) {
                    ProjectionBNG bng = (ProjectionBNG) finalTarget;
                    System.out.println("BNG: " + bng.getEasting() + ", " + bng.getNorthing());
                    System.out.println("Grid Reference: " + bng.getGridReference());
                }
                // Add other projection types as needed
            } else {
                System.out.println("Conversion to " + projectionType + " failed with error: " + errorCode);
            }
        }
    );

    ProjectionService.convert(wgs84Projection, targetProjection, progressListener);
}

```

This example demonstrates how to convert a single WGS84 coordinate to multiple projection systems and extract the relevant information from each converted projection.
