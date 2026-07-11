# Skyline — Weather App
 
A single-file web app that shows current weather conditions for your location or any city you search, with a background that visually reacts to the real weather (clear, cloudy, rain, snow, storm, fog) and time of day.
 
**Task:** Build a web page that fetches weather data from a weather API based on the user's location or a user-inputted location, and displays current conditions, temperature, and other relevant information.
 
---
 
## Files
 
| File | Purpose |
|---|---|
| `weather-app.html` | The complete app — HTML, CSS, and JavaScript in one file. |
| `README.md` | This document. |
 
---
 
## How to run it
 
1. Download `weather-app.html`.
2. Open it directly in any modern browser (double-click it, or drag it into a Chrome/Edge/Firefox tab).
3. Allow location access when prompted, or type a city name in the search box.
> **Note:** If you view this file inside an embedded preview (for example, a chat tool's built-in artifact panel), searches may silently fail. That's because embedded previews commonly block outbound network requests for security. The app detects this and shows a banner explaining it — opening the file as its own browser tab resolves it.
 
No installation, build step, or API key is required.
 
---
 
## How it works
 
### Data source
The app uses the **[Open-Meteo](https://open-meteo.com) API** for two things:
 
- **Geocoding** (`geocoding-api.open-meteo.com/v1/search`) — turns a typed city name into latitude/longitude, and powers the live search suggestions.
- **Forecast** (`api.open-meteo.com/v1/forecast`) — returns current temperature, feels-like temperature, humidity, wind speed, precipitation, weather code, and whether it's day or night, for a given latitude/longitude.
Open-Meteo was chosen because it's free and requires no API key or account, which keeps the app fully self-contained. See the "Using OpenWeatherMap instead" section below if your rubric asks for a specific, named API.
 
### Location input
- On page load, the browser's Geolocation API (`navigator.geolocation`) is used to request the user's current position automatically.
- A search box lets the user type any city; input is debounced (350ms) and queried against the geocoding endpoint to show live suggestions as they type.
- A location pin button lets the user re-trigger the "use my location" flow at any time.
### Weather display
Once coordinates are known, `fetchWeather()` retrieves current conditions and `render()` updates the UI:
- Big hero temperature (with a °C / °F toggle)
- Condition label and icon, derived from the WMO weather code Open-Meteo returns
- Feels-like temperature
- Humidity, wind speed, and precipitation in a small stats grid
- Last-updated timestamp
### Animated background
`renderSky()` maps the current weather category (clear / cloud / rain / snow / storm / fog) and day/night flag to:
- A gradient sky background
- Generated DOM elements with CSS animations: sun/moon and stars, drifting clouds, falling rain, snowflakes, a subtle lightning flash for storms, or drifting fog bands
### Error handling
- If geocoding returns no results, the user sees "No location found for '…'".
- If a fetch call fails outright, the app checks whether it's running inside an embedded iframe and tailors the message accordingly (sandbox warning vs. generic connectivity error).
- All failures are also logged to the browser console (`F12` → Console) for debugging.
---
 
## Using OpenWeatherMap instead
 
Some courses specifically expect **OpenWeatherMap**. To swap it in:
 
1. Get a free API key from [openweathermap.org](https://openweathermap.org/api).
2. Replace the `geocode()` function's URL with:
   `https://api.openweathermap.org/geo/1.0/direct?q={city}&limit=5&appid={YOUR_KEY}`
3. Replace the `fetchWeather()` function's URL with:
   `https://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&units=metric&appid={YOUR_KEY}`
4. Adjust the field names used in `render()` to match OpenWeatherMap's response shape (e.g. `main.temp`, `main.feels_like`, `main.humidity`, `wind.speed`, `weather[0].id` for the condition code).
Everything else — the UI, animations, unit toggle, and error handling — stays the same.
 
---
 
## Customization ideas
 
- Add an hourly or 5-day forecast strip using Open-Meteo's `hourly` / `daily` parameters.
- Persist the last-searched city (note: this app avoids `localStorage`/`sessionStorage` by design since some sandboxed environments don't support it — use a backend or a proper hosting environment if you want persistence).
- Add more cities as quick-select chips.
- Swap wind speed units (km/h ↔ mph).
---
 
## Tech summary
 
- Vanilla HTML/CSS/JavaScript — no frameworks, no build tools, no dependencies.
- Weather + geocoding: Open-Meteo (free, no key).
- Location: browser Geolocation API.
