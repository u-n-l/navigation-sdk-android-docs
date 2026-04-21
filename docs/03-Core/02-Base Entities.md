# Base entities

On this document, we present the simpler ones (coordinates, position, path, geographic areas), while in the following pages we cover the more complex ones (landmarks, markers, overlays, routes).

Reading this helps you understand and use the SDK effectively.

## UnlCoordinates[​](#coordinates "Direct link to UnlCoordinates")

The `UnlCoordinates` class is a core component designed to represent geographic positions with an optional altitude. The Navigation SDK for Android uses the [WGS](https://en.wikipedia.org/wiki/World_Geodetic_System) coordinates standard. Below is an overview of its functionality:

Key Features:

* **Latitude**: Specifies the north-south position. Range: -90.0 to +90.0.
* **Longitude**: Specifies the east-west position. Range: -180.0 to +180.0.
* **Altitude** (optional): Specifies the height in meters. Can be positive or negative.

>  📝 **Info**
>
> You can check if a `UnlCoordinates` instance is valid by using the `valid()` method, which returns `true` if the latitude and longitude are within their respective valid ranges.

### Instantiate UnlCoordinates[​](#instantiate-coordinates "Direct link to Instantiate UnlCoordinates")

To create a `UnlCoordinates` instance using latitude and longitude:

* Kotlin
* Java

```kotlin
// Kotlin
val coordinates = UnlCoordinates(37.7749, -122.4194) // San Francisco

```

```java
// Java
UnlCoordinates coordinates = new UnlCoordinates(37.7749, -122.4194); // San Francisco

```

### Distance between coordinates[​](#distance-between-coordinates "Direct link to Distance between coordinates")

To calculate the distance between two coordinates the `distance` method can be used. This method provides the distance between two coordinates in meters. It also takes into account the altitude if both coordinates have a value for this field.

* Kotlin
* Java

```kotlin
// Kotlin
val coord1 = UnlCoordinates(37.7749, -122.4194) // San Francisco
val coord2 = UnlCoordinates(34.0522, -118.2437) // Los Angeles
val distance = coord1.getDistance(coord2) // Distance in meters

```

```java
// Java
UnlCoordinates coord1 = new UnlCoordinates(37.7749, -122.4194); // San Francisco
UnlCoordinates coord2 = new UnlCoordinates(34.0522, -118.2437); // Los Angeles
double distance = coord1.getDistance(coord2); // Distance in meters

```

### Get Azimuth[​](#get-azimuth "Direct link to Get Azimuth")

The `getAzimuth` method calculates the azimuth (bearing) from the current coordinate to another coordinate. The azimuth is the angle in degrees from the north direction to the line connecting the two points, measured clockwise.

```text
   val coordinates = UnlCoordinates(37.7749, -122.4194)
   val northPoint = UnlCoordinates(90.0, 0.0)
   val azimuth = coordinates.getAzimuth(CnorthPoint) // Azimuth in degrees

```

## Path[​](#path "Direct link to Path")

A `UnlPath` represents a sequence of connected coordinates.

The `UnlPath` class is a core component for representing and managing paths on a map. It offers functionality for path creation, manipulation, and data export, allowing users to define paths and perform various operations programmatically.

**Key Features**

* **Path Creation & Management**

  * Paths can be created from data buffers in multiple formats (e.g., GPX, KML, GeoJSON).
  * Supports cloning paths in reverse order or between specific coordinates.

* **UnlCoordinates Handling**

  * Provides read-only access to internal coordinates lists.
  * Retrieves a coordinates based on a percentage along the path.

* **Path Properties**

  * **name**: Manage the name of the path.
  * **area**: Retrieve the bounding rectangle of the path.
  * **wayPoints**: Access waypoints along the path.

* **Export Functionality**
  * Export path data in various formats such as GPX, KML, and GeoJSON.

### Create a Path using coordinates[​](#create-a-path-using-coordinates "Direct link to Create a Path using coordinates")

* Kotlin
* Java

```kotlin
// Kotlin
val coordinatesList = UnlCoordinatesList(
    listOf(
        UnlCoordinates(37.774947, -122.419449),
        UnlCoordinates(37.776342, -122.417662),
        UnlCoordinates(37.777558, -122.416217)
    )
)
val path = UnlPath.produceWithCoords(UnlCoordinatesList(coordinatesList))

```

```java
// Java
UnlCoordinatesList coordinatesList = new UnlCoordinatesList(
    Arrays.asList(
        new UnlCoordinates(37.774947, -122.419449),
        new UnlCoordinates(37.776342, -122.417662),
        new UnlCoordinates(37.777558, -122.416217)
    )
);
UnlPath path = UnlPath.produceWithCoords(new UnlCoordinatesList(coordinatesList));

```

### Create a Path using data buffer[​](#create-a-path-using-data-buffer "Direct link to Create a UnlPath using data buffer")

* Kotlin
* Java

```kotlin
// Kotlin
val gpxData: ByteArray = ... // Load GPX data as ByteArray
val format : EPathFileFormat = EPathFileFormat.Gpx
val path = UnlPath.produceWithDataBuffer(gpxData, format)

```

```java
// Java
byte[] gpxData = ...; // Load GPX data as byte array
EPathFileFormat format = EPathFileFormat.Gpx;
UnlPath path = UnlPath.produceWithDataBuffer(gpxData, format);

```

The code above creates a `UnlPath` instance from GPX data provided as a `ByteArray`. The `EPathFileFormat.Gpx` enum value specifies the format of the data being used.

### Export a Path to data buffer[​](#export-a-path-to-data-buffer "Direct link to Export a Path to data buffer")

To export a Path to some given format (like GeoJson for example) you can proceed like this:

* Kotlin
* Java

```kotlin
// Kotlin
val format : EPathFileFormat = EPathFileFormat.GeoJson
path.exportAs(format) // Returns a DataBuffer or null if the export failed

```

```java
// Java
EPathFileFormat format = EPathFileFormat.GeoJson;
path.exportAs(format); // Returns a DataBuffer or null if the export failed

```

# Geographic areas

Geographic areas represent specific regions of the world and serve various purposes, such as centering, restricting searches to a specific region, geofencing, and more. Multiple entities can return a bounding box as a geographic area, defining the zone that contains the item.

> 📝 **Info**
>
> The geographic area types are:
> * **Rectangle Geographic Area**: Represents a rectangular area with the top and bottom sides parallel to the longitude and latitude lines.
> * **Circle Geographic Area**: Encompasses an area around a specific coordinates with a certain distance.
> * **Polygon Geographic Area**: Represents a complex area with high precision, ideal for more detailed geographic boundaries.

At the foundation of the geographic area hierarchy is the abstract UnlGeographicArea class, which defines the following operations:

| Method / Field                           | Description                                                                                        | Return Type               |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------- | ------------------------- |
| `boundingBox`                            | Get the bounding box of the geographic area, which is the smallest rectangle surrounding the area. | `RectangleGeographicArea` |
| `centerPoint`                            | Retrieves the center point of the geographic area, calculated as the geographic center.            | `UnlCoordinates`             |
| `convert(area:UnlGeographicArea)`           | Converts the geographic area to another type, if possible.                                         | `UnlGeographicArea?`         |
| `containsCoordinates(value:UnlCoordinates)` | Checks if the specified point is contained within the geographic area.                             | `Boolean`                 |
| `isDefault()`                            | Checks if the geographic area has default values.                                                  | `Boolean`                 |
| `isEmpty()`                              | Checks if the geographic area is empty.                                                            | `Boolean`                 |
| `type`                                   | Retrieves the specific type of the geographic area.                                                | `GeographicAreaType`      |
| `reset()`                                | Resets the geographic area to its default state.                                                   | `Unit`                    |

## Rectangle Geographic Area[​](#rectangle-geographic-area "Direct link to Rectangle Geographic Area")

The RectangleGeographicArea class represents a rectangular geographic area defined by two coordinates: the top-left and bottom-right corners. It provides operations to check for intersections, containment, and unions with other rectangles.

To create a new RectangleGeographicArea, the constructor can be used by providing the **top-left** and **bottom-right** coordinates.

* Kotlin
* Java

```kotlin
// Kotlin
val topLeft = UnlCoordinates(37.7749, -122.4194) // San Francisco
val bottomRight = UnlCoordinates(34.0522, -118.2437) // Los Angeles
val rectangleArea = RectangleGeographicArea(topLeft, bottomRight)

```

```java
// Java
UnlCoordinates topLeft = new UnlCoordinates(37.7749, -122.4194); // San Francisco
UnlCoordinates bottomRight = new UnlCoordinates(34.0522, -118.2437); // Los Angeles
RectangleGeographicArea rectangleArea = new RectangleGeographicArea(topLeft, bottomRight);

```

> 🚨 **Danger** <br/>
>
> Ensure that the top-left coordinate has a latitude greater than the bottom-right coordinate and a longitude less than the bottom-right coordinate. If this condition is not met, a **UnlError.InvalidInput** exception will be thrown.

# Circle Geographic Area

The CircleGeographicArea class represents a circular geographic area defined by a center point and a radius. It provides methods for checking if a point lies within the circle, calculating the bounding box, and more.

To create a new CircleGeographicArea, the constructor can be used by providing the center point and the distance in meters:

* Kotlin
* Java

```kotlin
// Kotlin
val radius = 5000// Radius in meters
val center = UnlCoordinates(37.7749, -122.4194) // San Francisco
val circleArea = CircleGeographicArea(center, radius)

```

```java
// Java
int radius = 5000; // Radius in meters
UnlCoordinates center = new UnlCoordinates(37.7749, -122.4194); // San Francisco
CircleGeographicArea circleArea = new CircleGeographicArea(center, radius);

```

## Polygon Geographic Area[​](#polygon-geographic-area "Direct link to Polygon Geographic Area")

The UnlPolygonGeographicArea class can be used to represent complex custom areas with a high level of precision.

They can be created by providing the list of coordinates:

* Kotlin
* Java

```kotlin
// Kotlin
val coordinatesList = UnlCoordinatesList(
    listOf(
        UnlCoordinates(37.774947, -122.419449),
        UnlCoordinates(37.776342, -122.417662),
        UnlCoordinates(37.777558, -122.416217),
    )
)
val polygonArea = UnlPolygonGeographicArea()
polygonArea.coordinates =coordinatesList

```

```java
// Java
UnlCoordinatesList coordinatesList = new UnlCoordinatesList(
    Arrays.asList(
        new UnlCoordinates(37.774947, -122.419449),
        new UnlCoordinates(37.776342, -122.417662),
        new UnlCoordinates(37.777558, -122.416217)
    )
);
UnlPolygonGeographicArea polygonArea = new UnlPolygonGeographicArea();
polygonArea.setCoordinates(coordinatesList);

```

> 📝 **Info**
>
> The polygon will be automatically closed, meaning that the last coordinate will be connected to the first one to form a closed shape.

> 🚨 **Danger**
>
> A polygon must have at least 3 coordinates to be valid.
