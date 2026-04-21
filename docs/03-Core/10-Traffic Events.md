# Traffic Events

The Navigation SDK for Android provides real-time information about traffic events, such as delays, which can occur in various forms.

When enabled and supported by the applied map style, traffic events are visually represented on the map as red overlays on affected road segments.

Based on the source of the event:

* Most traffic events are provided by the UNL servers if online and provide up-to-date traffic data
* The user can add custom user-defined roadblocks in order to blacklist certain road segments or areas.

Based on the impact zone:

* The traffic event can be a path-based traffic area, following the shape of a road.
* The traffic event can be an area-based traffic area, including a larger geographic area.

The central class for handling both traffic events and roadblocks is `UnlTrafficEvent`. Instances of this class can be obtained either through user interaction with the map (e.g., selecting a road segment) or as part of user-defined roadblock operations. For route-specific traffic data, the SDK provides the `RouteTrafficEvent` class, which extends `UnlTrafficEvent` by including detailed information relevant to a specific route. These events are provided by the route.

Traffic events, including delays and user-defined roadblocks, are fully integrated into the routing and navigation logic. This ensures that calculated routes dynamically account for traffic conditions and any restricted segments.

## TrafficEvent structure[​](#trafficevent-structure "Direct link to TrafficEvent structure")

The `UnlTrafficEvent` class has the following structure:

| Member                  | Type                       | Description           |
| ----------------------- | -------------------------- | --------------------------------------------------------------------------------------------------- |
| `isRoadblock()`         | Boolean                    | Returns `true` if the event represents a roadblock.                                                 |
| `delay`                 | Int                        | Estimated delay in seconds caused by the traffic event. For roadblocks it returns `-1`.             |
| `length`                | Int                        | Length in meters of the road segment affected by the traffic event.                                 |
| `referencePoint`        | `UnlCoordinates?`             | The central coordinate for the event. Returns `null` if not available.                              |
| `boundingBox`           | `RectangleGeographicArea?` | Geographical bounding box surrounding the event.                                                    |
| `description`           | `String?`                  | Human-readable description of the traffic event.                                                    |
| `eventClass`            | `ETrafficEventClass`       | Classification of the traffic event.                                                                |
| `eventSeverity`         | `ETrafficEventSeverity`    | Severity level of the event.                                                                        |
| `image`                 | `UnlImage?`                   | UnlImage of the traffic event. Returns `null` if not available.                                        |
| `previewUrl`            | `String?`                  | Returns a URL to preview the traffic event. Returns `null` if not available.                        |
| `isUserRoadblock()`     | `Boolean`                  | Returns `true` if the event is a user-defined roadblock.                                            |
| `affectedTransportMode` | Int                        | Returns all transport modes affected by the event as a bitmask.                                     |
| `startTime`             | `UnlTime?`                    | UTC start time of the traffic event, if available.                                                  |
| `endTime`               | `UnlTime?`                    | UTC end time of the traffic event, if available.                                                    |
| `hasOppositeSibling()`  | `Boolean`                  | Returns `true` if a sibling event exists in the opposite direction. Relevant for path-based events. |

## RouteTrafficEvent structure[​](#routetrafficevent-structure "Direct link to RouteTrafficEvent structure")

The `RouteTrafficEvent` class extends `UnlTrafficEvent` with the following members:

| Member                    | Type                      | Description          |
| ------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------ |
| `distanceToDestination`   | Int                       | Distance in meters from starting point on current route of the traffic event to the destination. |
| `from`                    | `UnlCoordinates?`            | Route traffic event start point.                                                                 |
| `to`                      | `UnlCoordinates?`            | Route traffic event end point.                                                                   |
| `fromLandmark`            | `Pair<UnlLandmark,Boolean>?` | Traffic event start point as landmark and a flag indicating if data is cached locally.           |
| `toLandmark`              | `Pair<UnlLandmark,Boolean>?` | Traffic event end point as landmark and a flag indicating if data is cached locally.             |
| `asyncUpdateToFromData()` | Unit                      | Updates the `from` and `to` landmarks' address and description info from the server.             |
| `cancelUpdate()`          | Unit                      | Cancels the pending async update request for landmark data.                                      |

## Usage[​](#usage "Direct link to Usage")

* Used to provide traffic information about a route. See the [Get ETA and Traffic information guide](/07-Routing/02-Get%20Started%20with%20Routing.md#get-eta-and-traffic-information) for more details.
* Used to implement user-defined roadblocks. See the [Get ETA and Traffic information guide](/07-Routing/02-Get%20Started%20with%20Routing.md#get-eta-and-traffic-information) for more details.

## Traffic Event Classes and Severities[​](#traffic-event-classes-and-severities "Direct link to Traffic Event Classes and Severities")

Traffic events provide insights into road conditions, delays, closures, and more.

> 📝 **Info**
>
>Some events provided by the `ETrafficEventClass` are:
>
>* TrafficRestrictions
>* Roadworks
>* Parking
>* Delays
>* Accidents
>* RoadConditions

<br/>

> 📝 **Info**
>
>`ETrafficEventSeverity` possible values are:
>
>* Stationary
>* Queuing
>* SlowTraffic
>* PossibleDelay
>* Unknown
