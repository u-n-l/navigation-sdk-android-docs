# Weather Service

The `UnlWeatherService` class contains methods for getting the current, hourly and daily forecasts.

## Get Current Weather Forecast[​](#get-current-weather-forecast "Direct link to Get Current Weather Forecast")

To retrieve the current weather forecast, use the `getCurrent` method of the `UnlWeatherService` class. Provide the coordinates for the desired location, and the forecast will be returned through the callback. The following example demonstrates how to fetch the current forecast for Paris coordinates.

* Kotlin
* Java

```kotlin
// Kotlin
val weatherService = UnlWeatherService()

val locationCoordinates = UnlCoordinates().apply {
    latitude = 48.864716
    longitude = 2.349014
}

val coordinatesList = UnlCoordinatesList().apply {
    add(locationCoordinates)
}

 weatherService.getCurrent(coordinatesList, onCompleted = { results, error, message ->
    if (error == UnlError.NoError) {
        // Process the current forecast results
        Log.d("Weather", "Forecast list size: ${results.size}")

        results.firstOrNull()?.let { locationForecast ->
            // Access forecast data
            locationForecast.forecast?.forEach { condition ->
                Log.d("Weather", "Condition: ${condition.description}")
            }
        }
    } else {
        Log.e("Weather", "Error getting current forecast: $message")
    }
})

```

```java
// Java
UnlWeatherService weatherService = new UnlWeatherService();

UnlCoordinates locationCoordinates = new UnlCoordinates();
locationCoordinates.setLatitude(48.864716);
locationCoordinates.setLongitude(2.349014);

UnlCoordinatesList coordinatesList = new UnlCoordinatesList();
coordinatesList.add(locationCoordinates);

weatherService.getCurrent(coordinatesList, (results, error, message) -> {
    if (error == UnlError.NoError) {
        // Process the current forecast results
        Log.d("Weather", "Forecast list size: " + results.size());

        if (!results.isEmpty()) {
            UnlLocationForecast locationForecast = results.get(0);
            // Access forecast data
            ConditionsList forecast = locationForecast.getForecast();
            if (forecast != null) {
                for (Conditions condition : forecast) {
                    Log.d("Weather", "Condition: " + condition.getDescription());
                }
            }
        }
    } else {
        Log.e("Weather", "Error getting current forecast: " + message);
    }
});

```

> 🚨 **DANGER**
>
> The API user is responsible for verifying whether the `UnlLocationForecast` contains any `Conditions`, and whether each `Condition` includes a `UnlWeatherParameter`. If data is unavailable for the specified location and time, the API may return empty lists of`Condition`s or `UnlWeatherParameter`s.

> 📝 **INFO**
>
> The result will contain as many `UnlLocationForecast` objects as the list size of given coordinates to `coords` parameter.

## Get Hourly Weather Forecast[​](#get-hourly-weather-forecast "Direct link to Get Hourly Weather Forecast")

To retrieve the hourly weather forecast, use the `getHourlyForecast` method of the `UnlWeatherService` class. Provide the coordinates for the desired location, and the forecast will be returned through the callback. The following example demonstrates how to fetch the hourly forecast for Paris coordinates.

* Kotlin
* Java

```kotlin
// Kotlin
val weatherService = UnlWeatherService()

val locationCoordinates = UnlCoordinates().apply {
    latitude = 48.864716
    longitude = 2.349014
}

val coordinatesList = UnlCoordinatesList().apply {
    add(locationCoordinates)
}

weatherService.getHourlyForecast(24, coordinatesList, onCompleted =  { results, error, message ->
    if (error == UnlError.NoError) {
        // Process the hourly forecast results
        Log.d("Weather", "Hourly forecast list size: ${results.size}")

        results.firstOrNull()?.forecast?.forEach { condition ->
            // Process each hourly condition
            val timestamp = condition.timestamp
            val description = condition.description

            condition.parameters?.forEach { parameter ->
                when (parameter.type) {
                    "Temperature" -> {
                        Log.d("Weather", "Temperature: ${parameter.value}${parameter.unit}")
                    }
                    "Humidity" -> {
                        Log.d("Weather", "Humidity: ${parameter.value}${parameter.unit}")
                    }
                }
            }
        }
    } else {
        Log.e("Weather", "Error getting hourly forecast: $message")
    }
})

```

```java
// Java
UnlWeatherService weatherService = new UnlWeatherService();

UnlCoordinates locationCoordinates = new UnlCoordinates();
locationCoordinates.setLatitude(48.864716);
locationCoordinates.setLongitude(2.349014);

UnlCoordinatesList coordinatesList = new UnlCoordinatesList();
coordinatesList.add(locationCoordinates);

weatherService.getHourlyForecast(24, coordinatesList, (results, error, message) -> {
    if (error == UnlError.NoError) {
        // Process the hourly forecast results
        Log.d("Weather", "Hourly forecast list size: " + results.size());

        if (!results.isEmpty()) {
            ConditionsList forecast = results.get(0).getForecast();
            if (forecast != null) {
                for (Conditions condition : forecast) {
                    // Process each hourly condition
                    UnlTime timestamp = condition.getTimestamp();
                    String description = condition.getDescription();

                    WeatherParameterList parameters = condition.getParameters();
                    if (parameters != null) {
                        for (UnlWeatherParameter parameter : parameters) {
                            if ("Temperature".equals(parameter.getType())) {
                                Log.d("Weather", "Temperature: " + parameter.getValue() + parameter.getUnit());
                            } else if ("Humidity".equals(parameter.getType())) {
                                Log.d("Weather", "Humidity: " + parameter.getValue() + parameter.getUnit());
                            }
                        }
                    }
                }
            }
        }
    } else {
        Log.e("Weather", "Error getting hourly forecast: " + message);
    }
});

```

You'll need to provide the number of hours for which the forecast is requested.

> 🚨 **DANGER**
>
> The number of requested hours must not exceed 240. Exceeding this limit will result in an empty response and an error of type `UnlError.outOfRange`.

## Get the Daily Forecast[​](#get-the-daily-forecast "Direct link to Get the Daily Forecast")

To retrieve the daily weather forecast, use the `getDailyForecast` method of the `UnlWeatherService` class. Provide the coordinates for the desired location, and the forecast will be returned through the callback. The following example demonstrates how to fetch the daily forecast for Paris coordinates.

* Kotlin
* Java

```kotlin
// Kotlin
val weatherService = UnlWeatherService()

val locationCoordinates = UnlCoordinates().apply {
    latitude = 48.864716
    longitude = 2.349014
}

val coordinatesList = UnlCoordinatesList().apply {
    add(locationCoordinates)
}

weatherService.getDailyForecast(10, coordinatesList, onCompleted =  { results, error, message ->
    if (error == UnlError.NoError) {
        // Process the daily forecast results
        Log.d("Weather", "Daily forecast list size: ${results.size}")

        results.firstOrNull()?.forecast?.forEach { condition ->
            // Process each daily condition
            val description = condition.description
            val daylight = condition.daylight

            condition.parameters?.forEach { parameter ->
                when (parameter.type) {
                    "TemperatureHigh" -> {
                        Log.d("Weather", "High temp: ${parameter.value}${parameter.unit}")
                    }
                    "TemperatureLow" -> {
                        Log.d("Weather", "Low temp: ${parameter.value}${parameter.unit}")
                    }
                    "Sunrise" -> {
                        // Convert timestamp to readable time
                        val sunriseTime = Date((parameter.value * 1000).toLong())
                        Log.d("Weather", "Sunrise: $sunriseTime")
                    }
                    "Sunset" -> {
                        // Convert timestamp to readable time
                        val sunsetTime = Date((parameter.value * 1000).toLong())
                        Log.d("Weather", "Sunset: $sunsetTime")
                    }
                }
            }
        }
    } else {
        Log.e("Weather", "Error getting daily forecast: $message")
    }
})

```

```java
// Java
UnlWeatherService weatherService = new UnlWeatherService();

UnlCoordinates locationCoordinates = new UnlCoordinates();
locationCoordinates.setLatitude(48.864716);
locationCoordinates.setLongitude(2.349014);

UnlCoordinatesList coordinatesList = new UnlCoordinatesList();
coordinatesList.add(locationCoordinates);

weatherService.getDailyForecast(10, coordinatesList, (results, error, message) -> {
    if (error == UnlError.NoError) {
        // Process the daily forecast results
        Log.d("Weather", "Daily forecast list size: " + results.size());

        if (!results.isEmpty()) {
            ConditionsList forecast = results.get(0).getForecast();
            if (forecast != null) {
                for (Conditions condition : forecast) {
                    // Process each daily condition
                    String description = condition.getDescription();
                    EDaylight daylight = condition.getDaylight();

                    WeatherParameterList parameters = condition.getParameters();
                    if (parameters != null) {
                        for (UnlWeatherParameter parameter : parameters) {
                            if ("TemperatureHigh".equals(parameter.getType())) {
                                Log.d("Weather", "High temp: " + parameter.getValue() + parameter.getUnit());
                            } else if ("TemperatureLow".equals(parameter.getType())) {
                                Log.d("Weather", "Low temp: " + parameter.getValue() + parameter.getUnit());
                            } else if ("Sunrise".equals(parameter.getType())) {
                                // Convert timestamp to readable time
                                Date sunriseTime = new Date((long) (parameter.getValue() * 1000));
                                Log.d("Weather", "Sunrise: " + sunriseTime);
                            } else if ("Sunset".equals(parameter.getType())) {
                                // Convert timestamp to readable time
                                Date sunsetTime = new Date((long) (parameter.getValue() * 1000));
                                Log.d("Weather", "Sunset: " + sunsetTime);
                            }
                        }
                    }
                }
            }
        }
    } else {
        Log.e("Weather", "Error getting daily forecast: " + message);
    }
});

```

You'll need to provide the number of days for which the forecast is requested.

> 🚨 **DANGER**
>
> The number of requested days must not exceed 10. Exceeding this limit will result in an empty response and an error of type `UnlError.outOfRange`.

## Get the Weather Forecast[​](#get-the-weather-forecast "Direct link to Get the Weather Forecast")

To retrieve the weather forecast at a specific time and coordinates, use the `getForecast` method of `UnlWeatherService` class. You'll need to provide a list of `UnlTimeDistanceCoordinate`, and the callback will retrieve as many `UnlLocationForecast` objects as the size of the coordinate list.

* Kotlin
* Java

```kotlin
// Kotlin
val weatherService = UnlWeatherService()

// Create coordinates for Paris
val coordinates = UnlCoordinates().apply {
    latitude = 48.864716
    longitude = 2.349014
}

// Create time-distance coordinate for forecast 2 days in the future
val timeDistanceCoordinate = UnlTimeDistanceCoordinate().also {
    it.coordinates = coordinates
    // Set duration to 2 days in the future (duration in seconds)
    it.timestamp = 2 * 24 * 60 * 60L // 2 days in seconds
}

val coordinatesList = UnlTimeDistanceCoordinateList().also { it.add(0,timeDistanceCoordinate) }

weatherService.getForecast(coordinatesList, onCompleted =  { results, error, message ->
    if (error == UnlError.NoError) {
        // Process the forecast results
        Log.d("Weather", "Forecast list size: ${results.size}")

        results.forEach { locationForecast ->
            locationForecast.forecast?.forEach { condition ->
                Log.d("Weather", "Forecast condition: ${condition.description}")

                // Process weather parameters
                condition.parameters?.forEach { parameter ->
                    Log.d("Weather", "${parameter.name}: ${parameter.value}${parameter.unit}")
                }
            }
        }
    } else {
        Log.e("Weather", "Error getting forecast: $message")
    }
})

```

```java
// Java
UnlWeatherService weatherService = new UnlWeatherService();

// Create coordinates for Paris
UnlCoordinates coordinates = new UnlCoordinates();
coordinates.setLatitude(48.864716);
coordinates.setLongitude(2.349014);

// Create time-distance coordinate for forecast 2 days in the future
UnlTimeDistanceCoordinate timeDistanceCoordinate = new UnlTimeDistanceCoordinate();
timeDistanceCoordinate.setCoordinates(coordinates);
// Set duration to 2 days in the future (duration in seconds)
timeDistanceCoordinate.setTimestamp(2 * 24 * 60 * 60L); // 2 days in seconds

UnlTimeDistanceCoordinateList coordinatesList = new UnlTimeDistanceCoordinateList();
coordinatesList.add(0, timeDistanceCoordinate);

weatherService.getForecast(coordinatesList, (results, error, message) -> {
    if (error == UnlError.NoError) {
        // Process the forecast results
        Log.d("Weather", "Forecast list size: " + results.size());

        for (UnlLocationForecast locationForecast : results) {
            ConditionsList forecast = locationForecast.getForecast();
            if (forecast != null) {
                for (Conditions condition : forecast) {
                    Log.d("Weather", "Forecast condition: " + condition.getDescription());

                    // Process weather parameters
                    WeatherParameterList parameters = condition.getParameters();
                    if (parameters != null) {
                        for (UnlWeatherParameter parameter : parameters) {
                            Log.d("Weather", parameter.getName() + ": " + parameter.getValue() + parameter.getUnit());
                        }
                    }
                }
            }
        }
    } else {
        Log.e("Weather", "Error getting forecast: " + message);
    }
});

```

> 📝 **INFO**
>
> The `duration` parameter in `UnlTimeDistanceCoordinate` specifies the relative time offset into the future (in seconds) for which the forecast is requested.

## Working with Weather Images[​](#working-with-weather-images "Direct link to Working with Weather Images")

Weather condition images are provided as `UnlImage` objects. Here's how to convert them to Android Bitmaps for display:

* Kotlin
* Java

```kotlin
// Kotlin
condition.image?.let { weatherImage ->
    // Convert Image to Bitmap
    val bitmap = weatherImage.asBitmap(100,100)

    // Use the bitmap in your ImageView
    //imageView.setImageBitmap(bitmap)
}

```

```java
// Java
UnlImage weatherImage = condition.getImage();
if (weatherImage != null) {
    // Convert Image to Bitmap
    Bitmap bitmap = weatherImage.asBitmap(100, 100);

    // Use the bitmap in your ImageView
    //imageView.setImageBitmap(bitmap);
}

```
