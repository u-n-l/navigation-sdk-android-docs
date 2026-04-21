# Base Entities

Weather-related functionalities are organized into distinct classes, each designed to encapsulate specific weather data. These include `UnlLocationForecast`, `Conditions`, and `UnlWeatherParameter`. This document provides a detailed explanation of each class and its purpose.

## UnlLocationForecast class[​](#locationforecast-class "Direct link to UnlLocationForecast class")

This class is responsible for retaining data such as the forecast update timestamp, the geographic location and forecast data.

| Property      | Type              | Description                     |
| ------------- | ----------------- | ------------------------------- |
| `updated`     | `UnlTime?`           | Forecast update timestamp (UTC) |
| `coordinates` | `UnlCoordinates?`    | Geographic location             |
| `forecast`    | `ConditionsList?` | Forecast data                   |

![Current Weather Forecase Explained](/assets/images/example_android_weather_base_entities-1c1419ae963055675f6bb613ee1c3d61.png "Current weather forecast explained")

**Current Weather Forecast explained**

## Conditions class[​](#conditions-class "Direct link to Conditions class")

This class is responsible for retaining weather conditions for a given timestamp.

| Property      | Type                    | Description                        |
| ------------- | ----------------------- | --------------------------------------------------------------- |
| `type`        | `String?`               | For possible values see add ref\[PredefinedParameterTypeValues] |
| `timestamp`   | `UnlTime?`                 | Timestamp for condition (UTC)                                   |
| `image`       | `UnlImage?`             | Image representation                                            |
| `description` | `String?`               | Description translated according to the current SDK language    |
| `daylight`    | `EDaylight`             | Daylight condition                                              |
| `parameters`  | `WeatherParameterList?` | Parameter list                                                  |

### PredefinedParameterTypeValues[​](#predefinedparametertypevalues "Direct link to PredefinedParameterTypeValues")

This class contains the common values for `Parameter.type` and `Conditions.type`.

| Property          | Description       | Unit |
| ----------------- | ----------------- | ---- |
| `airQuality`      | 'AirQuality'      | -    |
| `dewPoint`        | 'DewPoint'        | °C   |
| `feelsLike`       | 'FeelsLike'       | °C   |
| `humidity`        | 'Humidity'        | %    |
| `pressure`        | 'Pressure'        | mb   |
| `sunRise`         | 'Sunrise'         | -    |
| `sunSet`          | 'Sunset'          | -    |
| `temperature`     | 'Temperature'     | °C   |
| `uv`              | 'UV'              | -    |
| `visibility`      | 'Visibility'      | km   |
| `windDirection`   | 'WindDirection'   | °    |
| `windSpeed`       | 'WindSpeed'       | km/h |
| `temperatureLow`  | 'TemperatureLow'  | °C   |
| `temperatureHigh` | 'TemperatureHigh' | °C   |

> 🚨 **DANGER**
>
> The `UnlWeatherService` may return data with varying property types, depending on data availability. A response might include only a subset of the values listed above.

## UnlWeatherParameter class[​](#weatherparameter-class "Direct link to UnlWeatherParameter class")

Class responsible for containing weather parameter data.

| Property | Type      | Description                                                     |
| -------- | --------- | --------------------------------------------------------------- |
| `type`   | `String?` | For possible values see add ref\[PredefinedParameterTypeValues] |
| `value`  | `Double`  | Value                                                           |
| `name`   | `String?` | Name translated according to the current SDK language           |
| `unit`   | `String?` | Unit                                                            |

## Usage Example[​](#usage-example "Direct link to Usage Example")

Here's how to use these classes in Kotlin:

* Kotlin
* Java

```kotlin
// Kotlin
// Initialize UnlWeatherService
val weatherService = UnlWeatherService()

// Define coordinates for the location
val coordinates = UnlCoordinates().apply {
    latitude = 48.864716  // Paris latitude
    longitude = 2.349014  // Paris longitude
}
val coordinatesList = UnlCoordinatesList().apply { add(coordinates) }

// Get current weather forecast
weatherService.getCurrent(coordinatesList, onCompleted = { results, error, message ->
    if (error == GemError.NoError) {
        results.firstOrNull()?.let { locationForecast ->
            // Access forecast update timestamp
            val updated = locationForecast.updated

            // Access geographic location
            val location = locationForecast.coordinates

            // Access forecast data
            locationForecast.forecast?.forEach { condition ->
                // Access condition properties
                val type = condition.type
                val timestamp = condition.timestamp
                val description = condition.description
                val daylight = condition.daylight
                val image = condition.image

                // Access weather parameters
                condition.parameters?.forEach { parameter ->
                    val paramType = parameter.type
                    val paramValue = parameter.value
                    val paramName = parameter.name
                    val paramUnit = parameter.unit

                    // Process parameter data based on type
                    when (paramType) {
                        "Temperature" -> {
                            // Handle temperature data
                        }
                        "Humidity" -> {
                            // Handle humidity data
                        }
                        // Handle other parameter types...
                    }
                }
            }
        }
    } else {
        // Handle error
        Log.e("UnlWeatherService", "Error getting weather: $message")
    }
})

```

```java
//Java
// Initialize UnlWeatherService
UnlWeatherService weatherService = new UnlWeatherService();

// Define coordinates for the location
UnlCoordinates coordinates = new UnlCoordinates();
coordinates.setLatitude(48.864716);  // Paris latitude
coordinates.setLongitude(2.349014);  // Paris longitude

UnlCoordinatesList coordinatesList = new UnlCoordinatesList();
coordinatesList.add(coordinates);

// Get current weather forecast
weatherService.getCurrent(coordinatesList, (results, error, message) -> {
    if (error == GemError.NoError) {
        if (!results.isEmpty()) {
            UnlLocationForecast locationForecast = results.get(0);

            // Access forecast update timestamp
            UnlTime updated = locationForecast.getUpdated();

            // Access geographic location
            UnlCoordinates location = locationForecast.getCoordinates();

            // Access forecast data
            ConditionsList forecast = locationForecast.getForecast();
            if (forecast != null) {
                for (Conditions condition : forecast) {
                    // Access condition properties
                    String type = condition.getType();
                    UnlTime timestamp = condition.getTimestamp();
                    String description = condition.getDescription();
                    EDaylight daylight = condition.getDaylight();
                    UnlImage image = condition.getImage();

                    // Access weather parameters
                    WeatherParameterList parameters = condition.getParameters();
                    if (parameters != null) {
                        for (UnlWeatherParameter parameter : parameters) {
                            String paramType = parameter.getType();
                            double paramValue = parameter.getValue();
                            String paramName = parameter.getName();
                            String paramUnit = parameter.getUnit();

                            // Process parameter data based on type
                            if ("Temperature".equals(paramType)) {
                                // Handle temperature data
                            } else if ("Humidity".equals(paramType)) {
                                // Handle humidity data
                            }
                            // Handle other parameter types...
                        }
                    }
                }
            }
        }
    } else {
        // Handle error
        Log.e("UnlWeatherService", "Error getting weather: " + message);
    }
});

```
