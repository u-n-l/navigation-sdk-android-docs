# Handling UnlRoutePreferences

Before computing a route, we need to specify some route options using the `UnlRoutePreferences` class.

## Route Preferences structure[​](#route-preferences-structure "Direct link to Route Preferences structure")

### Core Routing Properties[​](#core-routing-properties "Direct link to Core Routing Properties")

| Property                         | Type                     | Default Value                    | Explanation                                                                 |
| -------------------------------- | ------------------------ | -------------------------------- | --------------------------------------------------------------------------- |
| **Basic Route Configuration**    |                          |                                  |                                                                             |
| routeType                        | EUnlRouteType               | EUnlRouteType.Fastest               | Preferred route type (Fastest, Shortest, Economic, Scenic).                 |
| transportMode                    | EUnlRouteTransportMode      | EUnlRouteTransportMode.Car          | Transport mode (Car, Lorry, Pedestrian, Bicycle, Public, SharedVehicles).   |
| resultDetails                    | ERouteResultDetails      | ERouteResultDetails.Full         | Level of details in the route result (Full, TimeDistance, Path).            |
| alternativesSchema               | ERouteAlternativesSchema | ERouteAlternativesSchema.Default | Schema for alternative routes (Default, Never, Always).                     |
| pathAlgorithm                    | ERoutePathAlgorithm      | ERoutePathAlgorithm.MagicEarth   | Algorithm used for path calculation (MagicEarth, ExternalCh).               |
| **Route Constraints**            |                          |                                  |                                                                             |
| maximumDistanceConstraint        | Boolean                  | true                             | Enables maximum distance constraints based on transport and result details. |
| alternativeRoutesBalancedSorting | Boolean                  | true                             | Balances sorting of alternative routes.                                     |
| accurateTrackMatch               | Boolean                  | true                             | Enables accurate track matching for routes.                                 |
| ignoreRestrictionsOverTrack      | Boolean                  | false                            | Ignores map restrictions in route-over-track mode.                          |
| **Timing and Schedule**          |                          |                                  |                                                                             |
| timestamp                        | UnlTime?                    | null (automatic)                 | Custom timestamp for PT routes (departure/arrival time).                    |
| isAutomaticTimestamp()           | Boolean                  | true                             | Returns if timestamp is set to automatic mode.                              |
| setIsAutomaticTimestamp()        | Function                 | -                                | Sets timestamp to automatic mode for PT routes.                             |
| **Geofencing**                   |                          |                                  |                                                                             |
| avoidGeofenceAreas               | ArrayList\<String>       | empty list                       | List of geofence area IDs to avoid during routing.                          |
| stickInsideGeofenceAreas         | ArrayList\<String>       | empty list                       | List of geofence area IDs to stay inside during routing.                    |

### Route Avoidance Options[​](#route-avoidance-options "Direct link to Route Avoidance Options")

| Property                   | Type              | Default Value          | Explanation                                         |
| -------------------------- | ----------------- | ---------------------- | --------------------------------------------------- |
| avoidMotorways             | Boolean           | false                  | Avoids motorways in the route.                      |
| avoidTollRoads             | Boolean           | false                  | Avoids toll roads in the route.                     |
| avoidFerries               | Boolean           | false                  | Avoids ferries in the route.                        |
| avoidUnpavedRoads          | Boolean           | false                  | Avoids unpaved roads in the route.                  |
| avoidCarpoolLanes          | Boolean           | false                  | Avoids carpool lanes.                               |
| avoidTurnAroundInstruction | Boolean           | false                  | Avoids turn-around instructions during navigation.  |
| avoidTraffic               | ETrafficAvoidance | ETrafficAvoidance.None | Traffic avoidance strategy (None, All, Roadblocks). |
| avoidBikingHillFactor      | Float             | 0.5                    | Factor to avoid biking hills (0.0-1.0).             |

### Vehicle Profile Configuration[​](#vehicle-profile-configuration "Direct link to Vehicle Profile Configuration")

| Property                     | Type                 | Default Value           | Explanation                                                           |
| ---------------------------- | -------------------- | ----------------------- | --------------------------------------------------------------------- |
| **Car Profile**              |                      |                         |                                                                       |
| carProfile                   | UnlCarProfile?          | null                    | Car-specific routing preferences (fuel type, mass, max speed).        |
| **Truck Profile**            |                      |                         |                                                                       |
| truckProfile                 | UnlTruckProfile?        | null                    | Truck-specific routing preferences (dimensions, weight, axle load).   |
| **Electric Vehicle Profile** |                      |                         |                                                                       |
| evProfile                    | EVProfile?           | null                    | Electric vehicle routing preferences (battery, charging, efficiency). |
| **Bike Profiles**            |                      |                         |                                                                       |
| bikeProfile                  | EUnlBikeProfile         | EUnlBikeProfile.Road       | Selected bike profile (Road, Cross, City, Mountain).                  |
| eBikeProfile                 | ElectricBikeProfile? | null                    | Electric bike profile configuration.                                  |
| defaultEBikeProfile          | ElectricBikeProfile? | null                    | Default electric bike profile.                                        |
| setBikeProfile()             | Function             | -                       | Sets bike profile with optional electric bike configuration.          |
| **Pedestrian Profile**       |                      |                         |                                                                       |
| pedestrianProfile            | EUnlPedestrianProfile   | EUnlPedestrianProfile.Walk | Pedestrian profile (Walk, Hike).                                      |

### Public Transport Options[​](#public-transport-options "Direct link to Public Transport Options")

| Property                     | Type               | Default Value               | Explanation                                                           |
| ---------------------------- | ------------------ | --------------------------- | --------------------------------------------------------------------- |
| algorithmType                | EUnlPTAlgorithmType   | EUnlPTAlgorithmType.Departure  | Algorithm type for PT routing (Departure, Arrival).                   |
| sortingStrategy              | EUnlPTSortingStrategy | EUnlPTSortingStrategy.BestTime | Strategy for sorting PT routes (BestTime, LeastWalk, LeastTransfers). |
| minimumTransferTimeInMinutes | Int                | 1                           | Minimum transfer time in minutes.                                     |
| maximumTransferTimeInMinutes | Int                | 300                         | Maximum transfer time in minutes.                                     |
| maximumWalkDistance          | Int                | 5000                        | Maximum walking distance in meters.                                   |
| routeTypePreferences         | Int                | 0 (no preference)           | PT route type preferences (bit flags for Bus, Underground, etc.).     |
| useBikes                     | Boolean            | false                       | Enables use of bikes in PT routes.                                    |
| useWheelchair                | Boolean            | false                       | Enables wheelchair-friendly PT routes.                                |
| routeGroupIdsEarlierLater    | ArrayList\<Int>?   | null                        | IDs for earlier/later route groups.                                   |

### Route Enhancement Features[​](#route-enhancement-features "Direct link to Route Enhancement Features")

| Property                       | Type             | Default Value   | Explanation                                                           |
| ------------------------------ | ---------------- | --------------- | --------------------------------------------------------------------- |
| **Terrain Profile**            |                  |                 |                                                                       |
| buildTerrainProfile            | Boolean          | false           | Enables building of terrain profile.                                  |
| setBuildTerrainProfile()       | Function         | -               | Sets terrain profile build with optional minimum elevation variation. |
| **Route Connections**          |                  |                 |                                                                       |
| setBuildConnections()          | Function         | -               | Enables building of route connections with max length.                |
| getBuildConnections()          | Function         | -               | Returns if route connections building is enabled.                     |
| getBuildConnectionsMaxLength() | Function         | -               | Returns maximum connection length in meters.                          |
| **Route Ranges (Isochrones)**  |                  |                 |                                                                       |
| routeRanges                    | ArrayList\<Int>? | null            | Route ranges for isochrone calculation.                               |
| routeRangesQuality             | Int              | 100             | Quality level for route ranges (0-100).                               |
| setRouteRanges()               | Function         | -               | Sets route ranges with quality level.                                 |
| **Departure Heading**          |                  |                 |                                                                       |
| departureHeading               | Double           | -1 (no heading) | Departure heading in degrees (0-360, -1 = no heading).                |
| setDepartureHeading()          | Function         | -               | Sets departure heading with accuracy.                                 |

### Emergency Vehicle Options[​](#emergency-vehicle-options "Direct link to Emergency Vehicle Options")

| Property                  | Type     | Default Value | Explanation                                            |
| ------------------------- | -------- | ------------- | ------------------------------------------------------ |
| emergencyVehicleMode      | Boolean  | false         | Enables emergency vehicle mode (read-only).            |
| setEmergencyVehicleMode() | Function | -             | Sets emergency vehicle mode with extra freedom levels. |

### Read-Only Properties[​](#read-only-properties "Direct link to Read-Only Properties")

| Property        | Type             | Default Value         | Explanation                                     |
| --------------- | ---------------- | --------------------- | ----------------------------------------------- |
| routeResultType | ERouteResultType | ERouteResultType.Path | Type of route result (Path, Range) - read-only. |

> 📝 **INFO**
>
> For **timestamp** usage with Public Transport routes, the SDK supports both automatic and manual timestamp setting. When `isAutomaticTimestamp()` returns true, the departure timestamp is set to the local time of the departing waypoint.
>
>* Kotlin
>* Java
>
>```kotlin
>// Kotlin
>// Set automatic timestamp (default behavior)
>routePreferences.setIsAutomaticTimestamp()
>
>// Set custom timestamp
>val customTime = UnlTime().apply {
>    // Set your desired departure/arrival time
>    setLocalTime() // or set specific time values
>}
>routePreferences.timestamp = customTime
>
>// Check if automatic timestamp is enabled
>val isAutomatic = routePreferences.isAutomaticTimestamp()
>
>```
>
>```java
>// Java
>// Set automatic timestamp (default behavior)
>routePreferences.setIsAutomaticTimestamp();
>
>// Set custom timestamp
>UnlTime customTime = new UnlTime();
>// Set your desired departure/arrival time
>customTime.setLocalTime(); // or set specific time values
>routePreferences.setTimestamp(customTime);
>
>// Check if automatic timestamp is enabled
>boolean isAutomatic = routePreferences.isAutomaticTimestamp();
>
>```

### Basic Usage Example[​](#basic-usage-example "Direct link to Basic Usage Example")

A short example of how to configure basic route preferences for the fastest car route with terrain profile:

* Kotlin
* Java

```kotlin
// Kotlin
val routePreferences = UnlRoutePreferences().apply {
    // Basic route configuration
    transportMode = EUnlRouteTransportMode.Car
    routeType = EUnlRouteType.Fastest
    resultDetails = ERouteResultDetails.Full

    // Enable terrain profile calculation
    setBuildTerrainProfile(true, 5.0f) // 5m minimum elevation variation

    // Avoid certain road types
    avoidMotorways = false
    avoidTollRoads = true
    avoidFerries = false

    // Set traffic avoidance
    avoidTraffic = ETrafficAvoidance.All
}

```

```java
// Java
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
// Basic route configuration
routePreferences.setTransportMode(EUnlRouteTransportMode.Car);
routePreferences.setRouteType(EUnlRouteType.Fastest);
routePreferences.setResultDetails(ERouteResultDetails.Full);

// Enable terrain profile calculation
routePreferences.setBuildTerrainProfile(true, 5.0f); // 5m minimum elevation variation

// Avoid certain road types
routePreferences.setAvoidMotorways(false);
routePreferences.setAvoidTollRoads(true);
routePreferences.setAvoidFerries(false);

// Set traffic avoidance
routePreferences.setAvoidTraffic(ETrafficAvoidance.All);

```

## Profiles structure[​](#profiles-structure "Direct link to Profiles structure")

### Car Profile[​](#car-profile "Direct link to Car Profile")

The `UnlCarProfile` class is responsible for defining car specific routing preferences. The available options are presented in the following table:

| Member   | Type      | Default                          | Description               |
| -------- | --------- | -------------------------------- | ------------------------- |
| fuelType | EUnlFuelType | EUnlFuelType.Petrol                 | Engine fuel type          |
| mass     | Int       | 0 - not considered in routing.   | Vehicle mass in kg.       |
| maxSpeed | Double    | 0.0 - not considered in routing. | Vehicle max speed in m/s. |

`EUnlFuelType` can have the following values: `Petrol`, `Diesel`, `LPG` (liquid petroleum gas), `Electric`.

By default, all fields except `fuelType` have default value 0, meaning they are not considered in the routing. `fuelType` by default is `EUnlFuelType.Petrol`.

* Kotlin
* Java

```kotlin
// Kotlin
val carProfile = UnlCarProfile(
    fuelType = EUnlFuelType.Diesel,
    mass = 1500, // kg
    maxSpeed = 50.0 // m/s
)

```

```java
// Java
UnlCarProfile carProfile = new UnlCarProfile(
    EUnlFuelType.Diesel,
    1500, // kg
    50.0  // m/s
);

```

### Truck Profile[​](#truck-profile "Direct link to Truck Profile")

The `UnlTruckProfile` class is responsible for defining truck specific routing preferences. The available options are presented in the following table:

| Member   | Type   | Default                         | Description               |
| -------- | ------ | ------------------------------- | ------------------------- |
| axleLoad | Int    | 0 - not considered in routing   | Truck axle load in kg.    |
| height   | Int    | 0 - not considered in routing   | Truck height in cm.       |
| length   | Int    | 0 - not considered in routing   | Truck length in cm.       |
| mass     | Int    | 0 - not considered in routing   | Vehicle mass in kg.       |
| maxSpeed | Double | 0.0 - not considered in routing | Vehicle max speed in m/s. |
| width    | Int    | 0 - not considered in routing   | Truck width in cm.        |

* Kotlin
* Java

```kotlin
// Kotin
val truckProfile = UnlTruckProfile(
    massKg = 3000,
    heightCm = 350,
    lengthCm = 1200,
    widthCm = 250,
    axleLoadKg = 11000,
    maxSpeedMs = 25.0
)

```

```java
// Java
UnlTruckProfile truckProfile = new UnlTruckProfile(
    3000,   // massKg
    350,    // heightCm
    1200,   // lengthCm
    250,    // widthCm
    11000,  // axleLoadKg
    25.0    // maxSpeedMs
);

```

### Electric Vehicle Profile[​](#electric-vehicle-profile "Direct link to Electric Vehicle Profile")

The `EVProfile` class is responsible for defining electric vehicle specific routing preferences. The available options are presented in the following table:

| Member              | Type    | Default | Description                                                     |
| ------------------- | ------- | ------- | --------------------------------------------------------------- |
| name                | String? | null    | Car model name.                                                 |
| towbarPossible      | Int     | 0       | Maximum weight available on vehicle towbar.                     |
| ports               | Int     | 0       | Supported charging ports (combination of EUnlEVChargingConnector). |
| departureSoc        | Float   | 0.0     | Departure battery state of charge (0-1).                        |
| destinationSoc      | Float   | 0.0     | Destination min battery state of charge (0-1).                  |
| chargerDestSoc      | Float   | 0.0     | Charger destination min battery state of charge (0-1).          |
| chargerDepSoc       | Float   | 0.0     | Charger departure max battery state of charge (0-1).            |
| chargerOverheadMins | Int     | 0       | Charger time overhead in minutes.                               |
| batteryHealth       | Float   | 0.0     | Battery health (0-1, where 1 is brand new).                     |
| batteryCapacity     | Int     | 0       | Battery capacity in watt hours.                                 |
| vehicleRange        | Int     | 0       | Vehicle range in meters.                                        |
| efficiency          | Int     | 0       | Consumption in Wh/km.                                           |
| fastCharge          | Int     | 0       | How many km charged in one hour (10-80 interval).               |

### Electric Bike Profile[​](#electric-bike-profile "Direct link to Electric Bike Profile")

The `ElectricBikeProfile` class is responsible for defining electric bike specific routing preferences. The available options are presented in the following table:

| Member                  | Type       | Default                     | Description                                                 |
| ----------------------- | ---------- | --------------------------- | ----------------------------------------------------------- |
| type                    | EUnlEBikeType | EUnlEBikeType.None             | E-bike type (None, Pedelec, PowerOnDemand).                 |
| bikeMass                | Float      | 0.0 - default value is used | Bike mass in kg.                                            |
| bikerMass               | Float      | 0.0 - default value is used | Biker mass in kg.                                           |
| auxConsumptionDay       | Float      | 0.0 - default value is used | Bike auxiliary power consumption during day in Watts.       |
| auxConsumptionNight     | Float      | 0.0 - default value is used | Bike auxiliary power consumption during night in Watts.     |
| refSpeed                | Float      | 0.0 - default value is used | Reference speed in m/s.                                     |
| ignoreLegalRestrictions | Boolean    | false                       | Ignore country-based legal restrictions related to e-bikes. |

* Kotlin
* Java

```kotlin
// Kotlin
val electricBikeProfile = ElectricBikeProfile(
    bikeType = EUnlEBikeType.Pedelec,
    bikeMassKg = 25.0f,
    bikerMassKg = 70.0f,
    auxConsDayW = 10.0f,
    auxConsNightW = 15.0f
).apply {
    ignoreLegalRestrictions = false
}

// Set bike profile with electric bike configuration
routePreferences.setBikeProfile(EUnlBikeProfile.City, electricBikeProfile)

```

```java
// Java
ElectricBikeProfile electricBikeProfile = new ElectricBikeProfile(
    EUnlEBikeType.Pedelec,
    25.0f,  // bikeMassKg
    70.0f,  // bikerMassKg
    10.0f,  // auxConsDayW
    15.0f   // auxConsNightW
);
electricBikeProfile.setIgnoreLegalRestrictions(false);

// Set bike profile with electric bike configuration
routePreferences.setBikeProfile(EUnlBikeProfile.City, electricBikeProfile);

```

## Computing truck routes[​](#computing-truck-routes "Direct link to Computing truck routes")

To compute routes for trucks we can write code like the following by initializing the `truckProfile` field of `UnlRoutePreferences`:

* Kotlin
* Java

```kotlin
// Kotlin
// Define the departure landmark
val departureLandmark = UnlLandmark().apply {
    coordinates = UnlCoordinates(48.87126, 2.33787)
}

// Define the destination landmark
val destinationLandmark = UnlLandmark().apply {
    coordinates = UnlCoordinates(51.4739, -0.0302)
}

// Create truck profile
val truckProfile = UnlTruckProfile(
    massKg = 3000,      // kg
    heightCm = 180,     // cm
    lengthCm = 500,     // cm
    widthCm = 200,      // cm
    axleLoadKg = 1500,  // kg
    maxSpeedMs = 16.67  // m/s (60 km/h)
)

// Define the route preferences with truck profile and lorry transport mode
val routePreferences = UnlRoutePreferences().apply {
    this.truckProfile = truckProfile
    transportMode = EUnlRouteTransportMode.Lorry // <- This field is crucial
}

// Create waypoints list
val waypoints = arrayListOf(departureLandmark, destinationLandmark)

// Create routing service and calculate route
val routingService = UnlRoutingService(
    preferences = routePreferences,
    onCompleted = { routes, errorCode, hint ->
        if (errorCode == UnlError.Success) {
            // Handle successful route calculation
            println("Number of routes: ${routes.size}")
        } else {
            // Handle error
            println("Error calculating route: $errorCode - $hint")
        }
    }
)

val result = routingService.calculateRoute(waypoints)

```

```java
// Java
// Define the departure landmark
UnlLandmark departureLandmark = new UnlLandmark();
departureLandmark.setCoordinates(new UnlCoordinates(48.87126, 2.33787));

// Define the destination landmark
UnlLandmark destinationLandmark = new UnlLandmark();
destinationLandmark.setCoordinates(new UnlCoordinates(51.4739, -0.0302));

// Create truck profile
UnlTruckProfile truckProfile = new UnlTruckProfile(
    3000,   // massKg
    180,    // heightCm
    500,    // lengthCm
    200,    // widthCm
    1500,   // axleLoadKg
    16.67   // maxSpeedMs (60 km/h)
);

// Define the route preferences with truck profile and lorry transport mode
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
routePreferences.setTruckProfile(truckProfile);
routePreferences.setTransportMode(EUnlRouteTransportMode.Lorry); // <- This field is crucial

// Create waypoints list
ArrayList<UnlLandmark> waypoints = new ArrayList<>();
waypoints.add(departureLandmark);
waypoints.add(destinationLandmark);

// Create routing service and calculate route
UnlRoutingService routingService = new UnlRoutingService(
    routePreferences,
    (routes, errorCode, hint) -> {
        if (errorCode == UnlError.Success) {
            // Handle successful route calculation
            System.out.println("Number of routes: " + routes.size());
        } else {
            // Handle error
            System.out.println("Error calculating route: " + errorCode + " - " + hint);
        }
    }
);

int result = routingService.calculateRoute(waypoints);

```

## Computing caravan routes[​](#computing-caravan-routes "Direct link to Computing caravan routes")

Certain vehicles, such as caravans or trailers, may be restricted on some roads due to their size or weight, yet still permitted on roads where trucks are prohibited.

To calculate routes for caravans or trailers, we can use the `truckProfile` field of `UnlRoutePreferences` with the appropriate dimensions and weight.

* Kotlin
* Java

```kotlin
// Kotlin
// Define the departure landmark
val departureLandmark = UnlLandmark().apply {
    coordinates = UnlCoordinates(48.87126, 2.33787)
}

// Define the destination landmark
val destinationLandmark = UnlLandmark().apply {
    coordinates = UnlCoordinates(51.4739, -0.0302)
}

// Create caravan profile using UnlTruckProfile
val caravanProfile = UnlTruckProfile(
    heightCm = 180,     // cm
    lengthCm = 500,     // cm
    widthCm = 200,      // cm
    axleLoadKg = 1500   // kg
)

// Define the route preferences with caravan profile and car transport mode
val routePreferences = UnlRoutePreferences().apply {
    this.truckProfile = caravanProfile
    transportMode = EUnlRouteTransportMode.Car // <- This field is crucial to distinguish caravan from truck
}

// Create waypoints list
val waypoints = arrayListOf(departureLandmark, destinationLandmark)

// Create routing service and calculate route
val routingService = UnlRoutingService(
    preferences = routePreferences,
    onCompleted = { routes, errorCode, hint ->
        // Handle results
    }
)

val result = routingService.calculateRoute(waypoints)

```

```java
// Java
// Define the departure landmark
UnlLandmark departureLandmark = new UnlLandmark();
departureLandmark.setCoordinates(new UnlCoordinates(48.87126, 2.33787));

// Define the destination landmark
UnlLandmark destinationLandmark = new UnlLandmark();
destinationLandmark.setCoordinates(new UnlCoordinates(51.4739, -0.0302));

// Create caravan profile using UnlTruckProfile
UnlTruckProfile caravanProfile = new UnlTruckProfile(
    0,      // massKg
    180,    // heightCm
    500,    // lengthCm
    200,    // widthCm
    1500,   // axleLoadKg
    0.0     // maxSpeedMs
);

// Define the route preferences with caravan profile and car transport mode
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
routePreferences.setTruckProfile(caravanProfile);
routePreferences.setTransportMode(EUnlRouteTransportMode.Car); // <- This field is crucial to distinguish caravan from truck

// Create waypoints list
ArrayList<UnlLandmark> waypoints = new ArrayList<>();
waypoints.add(departureLandmark);
waypoints.add(destinationLandmark);

// Create routing service and calculate route
UnlRoutingService routingService = new UnlRoutingService(
    routePreferences,
    (routes, errorCode, hint) -> {
        // Handle results
    }
);

int result = routingService.calculateRoute(waypoints);

```

At least one of the fields `height`, `length`, `width` or `axleLoad` must be set to a non-zero value in order for the settings to be taken into account during routing. If all these fields are set to 0 then a normal car route will be calculated.

## Computing electric vehicle routes[​](#computing-electric-vehicle-routes "Direct link to Computing electric vehicle routes")

To compute routes for electric vehicles, you can use the `evProfile` field of `UnlRoutePreferences`:

* Kotlin
* Java

```kotlin
// Kotlin
// Create EV profile
val evProfile = EVProfile().apply {
    name = "Tesla Model 3"
    departureSoc = 0.8f         // 80% charge at departure
    destinationSoc = 0.2f       // Minimum 20% charge at destination
    batteryCapacity = 75000     // 75 kWh in Wh
    vehicleRange = 500000       // 500 km in meters
    efficiency = 150            // 150 Wh/km
    fastCharge = 250            // 250 km per hour charging
    ports = EUnlEVChargingConnector.Type2.value or EUnlEVChargingConnector.CSS2.value
}

// Define the route preferences with EV profile
val routePreferences = UnlRoutePreferences().apply {
    this.evProfile = evProfile
    transportMode = EUnlRouteTransportMode.Car
}

```

```java
// Java
// Create EV profile
EVProfile evProfile = new EVProfile();
evProfile.setName("Tesla Model 3");
evProfile.setDepartureSoc(0.8f);         // 80% charge at departure
evProfile.setDestinationSoc(0.2f);       // Minimum 20% charge at destination
evProfile.setBatteryCapacity(75000);     // 75 kWh in Wh
evProfile.setVehicleRange(500000);       // 500 km in meters
evProfile.setEfficiency(150);            // 150 Wh/km
evProfile.setFastCharge(250);            // 250 km per hour charging
evProfile.setPorts(EUnlEVChargingConnector.Type2.getValue() | EUnlEVChargingConnector.CSS2.getValue());

// Define the route preferences with EV profile
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
routePreferences.setEvProfile(evProfile);
routePreferences.setTransportMode(EUnlRouteTransportMode.Car);

```

## Setting departure heading[​](#setting-departure-heading "Direct link to Setting departure heading")

You can specify a departure heading to influence the initial direction of the route:

* Kotlin
* Java

```kotlin
// Kotlin
val routePreferences = UnlRoutePreferences().apply {
    // Set departure heading to 45 degrees with 25 degrees accuracy
    setDepartureHeading(45.0, 25.0)
}

```

```java
// Java
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
// Set departure heading to 45 degrees with 25 degrees accuracy
routePreferences.setDepartureHeading(45.0, 25.0);

```

## Building terrain profile and connections[​](#building-terrain-profile-and-connections "Direct link to Building terrain profile and connections")

To get detailed terrain information and route connections:

* Kotlin
* Java

```kotlin
// Kotlin
val routePreferences = UnlRoutePreferences().apply {
    // Enable terrain profile with minimum elevation variation
    setBuildTerrainProfile(true, 5.0f) // 5 meters minimum variation

    // Enable route connections with maximum connection length
    setBuildConnections(true, 1000) // 1000 meters max connection length
}

```

```java
// Java
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
// Enable terrain profile with minimum elevation variation
routePreferences.setBuildTerrainProfile(true, 5.0f); // 5 meters minimum variation

// Enable route connections with maximum connection length
routePreferences.setBuildConnections(true, 1000); // 1000 meters max connection length

```

## Working with route ranges (Isochrones)[​](#working-with-route-ranges-isochrones "Direct link to Working with route ranges (Isochrones)")

To calculate isochrone areas (reachable areas within specific time/distance):

* Kotlin
* Java

```kotlin
// Kotlin
val routePreferences = UnlRoutePreferences().apply {
    transportMode = EUnlRouteTransportMode.Car
    routeType = EUnlRouteType.Fastest

    // Set route ranges for isochrone calculation
    // For fastest routes: values are in seconds
    // For shortest routes: values are in meters
    val ranges = arrayListOf(300, 600, 900) // 5, 10, 15 minutes
    setRouteRanges(ranges, 80) // 80% quality
}

```

```java
// Java
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
routePreferences.setTransportMode(EUnlRouteTransportMode.Car);
routePreferences.setRouteType(EUnlRouteType.Fastest);

// Set route ranges for isochrone calculation
// For fastest routes: values are in seconds
// For shortest routes: values are in meters
ArrayList<Integer> ranges = new ArrayList<>(Arrays.asList(300, 600, 900)); // 5, 10, 15 minutes
routePreferences.setRouteRanges(ranges, 80); // 80% quality

```

## Public Transport routing[​](#public-transport-routing "Direct link to Public Transport routing")

For public transport routes, you can configure specific PT preferences:

* Kotlin
* Java

```kotlin
// Kotlin
val routePreferences = UnlRoutePreferences().apply {
    transportMode = EUnlRouteTransportMode.Public
    algorithmType = EUnlPTAlgorithmType.Departure
    sortingStrategy = EUnlPTSortingStrategy.BestTime
    minimumTransferTimeInMinutes = 2
    maximumTransferTimeInMinutes = 240
    maximumWalkDistance = 1000  // 1 km
    useWheelchair = true
    useBikes = false

    // Set route type preferences (combine multiple types with bitwise OR)
    routeTypePreferences = EUnlPTRouteTypePreference.Bus.value or
                          EUnlPTRouteTypePreference.Underground.value
}

```

```java
// Java
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
routePreferences.setTransportMode(EUnlRouteTransportMode.Public);
routePreferences.setAlgorithmType(EUnlPTAlgorithmType.Departure);
routePreferences.setSortingStrategy(EUnlPTSortingStrategy.BestTime);
routePreferences.setMinimumTransferTimeInMinutes(2);
routePreferences.setMaximumTransferTimeInMinutes(240);
routePreferences.setMaximumWalkDistance(1000);  // 1 km
routePreferences.setUseWheelchair(true);
routePreferences.setUseBikes(false);

// Set route type preferences (combine multiple types with bitwise OR)
routePreferences.setRouteTypePreferences(
    EUnlPTRouteTypePreference.Bus.getValue() |
    EUnlPTRouteTypePreference.Underground.getValue()
);

```

## Calculate round trips[​](#calculate-round-trips "Direct link to Calculate round trips")

Generate routes that start and end at the same location using roundtrip parameters.

**Key differences from normal routing:**

* **Waypoint requirements** - Normal routes require at least two waypoints. Roundtrips need only one departure point; additional waypoints are ignored. The algorithm generates multiple output waypoints automatically
* **Determinism** - Normal routing produces the same route when inputs remain unchanged. Roundtrip generation is random by design, creating different routes each time. Use a seed value for deterministic results
* **Preferences** - All normal routing preferences apply, including vehicle profiles and fitness factors

### Range types[​](#range-types "Direct link to Range types")

The `EUnlRangeType` enumeration specifies units for the `range` parameter:

| EUnlRangeType value | Description     |
| ---------------- | ------------------------------------------------------------------------------- |
| Default          | Uses `DistanceBased` for shortest routes; `TimeBased` for all other route types |
| DistanceBased    | Distance measured in meters                                                     |
| TimeBased        | Duration measured in seconds                                                    |
| (EnergyBased)    | Not currently supported                                                         |

> 💡 **TIP**
>
> Specify `DistanceBased` or `TimeBased` explicitly to avoid confusion. A value of 10,000 means 10 km for distance-based requests but \~3 hours for time-based requests.

### Roundtrip parameters[​](#roundtrip-parameters "Direct link to Roundtrip parameters")

Configure roundtrips using **RoundTripParameters**:

| Parameter  | Type       | Description       |
| ---------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------- |
| range      | Int        | Target length or duration. The algorithm approximates this value but does not guarantee exact matches                             |
| rangeType  | EUnlRangeType | Units for the range value (distance or time)     |
| randomSeed | Int        | Set to `0` for random generation each time. Any other value produces deterministic results when other parameters remain unchanged |

### Create a round trip route[​](#create-a-round-trip-route "Direct link to Create a round trip route")

Set `roundTripParameters` in **UnlRoutePreferences** with a non-zero `range` value:

* Kotlin
* Java

```kotlin
// Kotlin
// Define the departure landmark
val departureLandmark = UnlLandmark().apply {
    coordinates = UnlCoordinates(48.85682, 2.34375)
}

// Define round trip preferences
val tripPreferences = RoundTripParameters(
    range = 5000,
    rangeType = EUnlRangeType.DistanceBased,
    randomSeed = 0  // 0 for random generation, other values for deterministic results
)

// Define route preferences to include round trip parameters
val routePreferences = UnlRoutePreferences().apply {
    transportMode = EUnlRouteTransportMode.Bicycle
    roundTripParameters = tripPreferences
}

// Create routing service and calculate round trip route
// Use only the departure landmark - additional waypoints are ignored
val routingService = UnlRoutingService(
    preferences = routePreferences,
    onCompleted = { routes, errorCode, hint ->
        if (errorCode == UnlError.Success) {
            // Handle successful round trip calculation
            println("Number of routes: ${routes.size}")
        } else {
            // Handle error
            println("Error calculating round trip: $errorCode - $hint")
        }
    }
)

val waypoints = arrayListOf(departureLandmark)
val result = routingService.calculateRoute(waypoints)

```

```java
// Java
// Define the departure landmark
UnlLandmark departureLandmark = new UnlLandmark();
departureLandmark.setCoordinates(new UnlCoordinates(48.85682, 2.34375));

// Define round trip preferences
RoundTripParameters tripPreferences = new RoundTripParameters(
    5000,
    EUnlRangeType.DistanceBased,
    0  // 0 for random generation, other values for deterministic results
);

// Define route preferences to include round trip parameters
UnlRoutePreferences routePreferences = new UnlRoutePreferences();
routePreferences.setTransportMode(EUnlRouteTransportMode.Bicycle);
routePreferences.setRoundTripParameters(tripPreferences);

// Create routing service and calculate round trip route
// Use only the departure landmark - additional waypoints are ignored
UnlRoutingService routingService = new UnlRoutingService(
    routePreferences,
    (routes, errorCode, hint) -> {
        if (errorCode == UnlError.Success) {
            // Handle successful round trip calculation
            System.out.println("Number of routes: " + routes.size());
        } else {
            // Handle error
            System.out.println("Error calculating round trip: " + errorCode + " - " + hint);
        }
    }
);

ArrayList<UnlLandmark> waypoints = new ArrayList<>();
waypoints.add(departureLandmark);
int result = routingService.calculateRoute(waypoints);

```

> 🚨 **DANGER**
>
> If more than one waypoint is provided in a round trip calculation, only the first is considered; others are ignored.
