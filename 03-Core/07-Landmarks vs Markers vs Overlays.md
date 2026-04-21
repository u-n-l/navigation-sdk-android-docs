# Landmarks vs Markers vs Overlays

When building a sophisticated mapping application, choosing the right type of object to use for your specific needs is crucial. To assist in making an informed decision, we compare the three core mapping entities in the table below:

| Characteristic |Landmarks | Markers | Overlays |
| --------------------------------- | -------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| Select from map                       | basic selection using `cursorSelectionLandmarks()` | advanced selection using `cursorSelectionMarkers()`, providing matched marker and the collection it belongs to, plus positional details such as the marker’s index within its collection, which part/segment or vertex was hit and so on | basic selection using `cursorSelectionOverlayItems()` |
| On the map by default                 | yes | no | yes, if present within the style |
| Customizable render settings          | basic level of customization using highlights | high level of customization using `UnlMarkerRenderSettings` | within the style (in Studio). Also allows customization using highlights |
| Visibility and layering               | toggleable based on the category and store | can individually be changed  | toggleable based on the category and overlay |
| Searchable                            | yes | no  | yes |
| Can be used for route calculation     | yes | no | no  |
| Can be used for alarms                | yes | no | yes |
| Create custom items                   | programmatically within the client application | programmatically within the client application | using uploaded GeoJSON Data Sets (in Studio); they cannot be created within the client application(1) |
| Available offline                     | yes  | yes | no, with some exceptions |
| Shared among users                    | yes, only for predefined landmarks. Changes made to landmarks and custom landmarks are local | no | yes, the overlay items are accessible by all users given they have the correct style applied |
| Extra info                            | address contact info, category, etc. | no | data with flexible structure (`ParameterList`) |

> 📝 **Info**
>
>Social reports can be created and modified by app clients and are accessible to all other users.
