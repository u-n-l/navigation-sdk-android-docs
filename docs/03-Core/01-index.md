# Core

The following articles provide comprehensive guidance on working with landmarks and markers. These resources offer a detailed understanding of predefined and user-defined points of interest, rich metadata, and dynamic annotations.

Additionally, the documentation covers the integration of layered map data through overlays and the creation of navigable routes for various use cases. Finally, you will find information on real-time navigation system entities, including support for turn-by-turn guidance and route simulation to enhance the user experience.

- [Base Entities](/03-Core/02-Base%20Entities.md)
   - Simple entities like coordinates, position, path, geographic areas are covered on this guide.
- [Positions](/03-Core/03-Positions.md)
   - The UnlPositionData and ImprovedPostionData classes provide a comprehensive representation of geographical and movement data for GPS-based systems. With robust support for position quality assessment and timestamped data, it is well-suited for navigation and sensor-driven applications.
- [Landmarks](/03-Core/04-Landmarks.md)
   - A landmark represents a significant, categorized location with rich metadata, providing structured context about a place.
- [Markers](/03-Core/05-Markers.md)
   - A marker is a visual representation (such as an icon or a geometry, like a polyline or polygon) placed at a specific geographic location on a map to indicate an important point of interest, event, or location.
- [Overlays](/03-Core/06-Overlays.md)
   - An Overlay is an additional map layer, either default or user-defined, with data stored on UNL servers, accessible in online and offline modes, with few exceptions.
- [Landmarks vs Markers vs Overlays](/03-Core/07-Landmarks%20vs%20Markers%20vs%20Overlays.md)
   - When building a sophisticated mapping application, choosing the right type of object to use for your specific needs is crucial. To assist in making an informed decision, we compare the three core mapping entities.
- [Routes](/03-Core/08-Routes.md)
   - A Route usually represents a navigable path between two or more landmarks (waypoints). It includes data such as distance, estimated time, and navigation instructions.
- [Navigation Instructions](/03-Core/09-Navigation%20Instructions.md)
   - The Navigation SDK for Android offers comprehensive real-time navigation guidance, providing detailed information on the current and upcoming route, including road details, street names, speed limits, and turn directions.
- [Traffic Events](/03-Core/10-Traffic%20Events.md)
   - The Navigation SDK for Android provides real-time information about traffic events, such as delays, which can occur in various forms.
- [Images](/03-Core/11-Images.md)
   - The images are represented in an abstract form which is not directly drawable on the UI.
