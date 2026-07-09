# 🌍 Sirgon Globe
### Weather and Time, Anywhere in the World.

<p align="center">
  <img src="media/sirgon-globe-demo.gif" alt="Sirgon Globe Demo" width="900">
</p>

<p align="center">

**A modern weather and world clock dashboard card for Home Assistant.**

Beautiful Apple Weather–inspired visuals combined with true multi-location time awareness.

[![HACS](https://img.shields.io/badge/HACS-Default-blue.svg)](https://hacs.xyz/)
[![License](https://img.shields.io/github/license/FernandoAFOliveira/sirgon-globe)](LICENSE)

</p>

---

# Overview

Sirgon Globe is a custom Lovelace card for Home Assistant designed for users who need to monitor weather and local time across multiple locations around the world.

Whether your family lives in different countries, you manage remote offices, monitor vacation homes, or simply enjoy following the weather worldwide, every card can display an independent weather source together with its own local clock.

Unlike traditional weather cards that rely on the browser's timezone or fixed UTC offsets, Sirgon Globe integrates directly with Home Assistant WorldClock entities, allowing every card to automatically follow local daylight-saving time rules.

The result is a dashboard where every location always displays the correct local weather **and** the correct local time.

---

# Why Sirgon Globe?

Many families today are spread across multiple countries.

Knowing the weather is only half the story.

You also want to know:

- What time is it there?
- Is daylight saving currently active?
- Is it morning or late at night?
- Is this a good time to call?

Sirgon Globe was designed around that idea.

Each card is completely independent.

Every location can have its own:

- Weather integration
- Forecast
- Local weather station
- WorldClock entity
- Daylight-saving rules

without depending on the browser's timezone.

---

# ✨ Features

## 🌤 Weather

- Apple Weather–inspired interface
- Beautiful animated backgrounds
- Dynamic day/night transitions
- Animated rain, snow, fog, clouds and lightning
- Realistic moon rendering
- Hourly and daily forecasts
- Forecast strip
- Lens flare effects
- Aurora Borealis and Aurora Australis
- Shooting stars during clear nights

---

## 🌍 World Time

Sirgon Globe introduces native multi-location time support.

- Independent clock for every card
- Native Home Assistant WorldClock integration
- Automatic daylight-saving handling
- No manual UTC offsets
- Multiple locations on the same dashboard
- Completely browser timezone independent

---

## 🌡 Local Weather Stations

Combine forecast providers with your own sensors.

Supported supplemental sensors include:

- Temperature
- Feels Like
- Humidity
- Wind Speed
- Wind Direction
- Pressure
- UV Index
- Precipitation

---

## ⚙️ Configuration

- Visual editor
- Live card preview
- Multiple weather providers
- Multiple weather sources
- Local weather stations
- Multiple forecast types
- Hourly / Daily switching
- Custom tap actions
- Multilingual support

---

# 📷 Screenshots

## Family Dashboard

![Dashboard](media/sirgon-globe-preview.png)

---

## Visual Configuration Editor

The integrated editor makes configuring multiple locations simple.

- Weather providers
- WorldClock entities
- Local weather stations
- Forecast options
- Live preview

---

# 🌍 WorldClock Integration

One of Sirgon Globe's defining features is native support for Home Assistant WorldClock entities.

Example:

```yaml
type: custom:sirgon-globe
entity: weather.forecast_porto

local_time: sensor.europe_lisbon
```

Unlike traditional weather cards, Sirgon Globe does **not** calculate local time using fixed UTC offsets.

Instead, it reads the WorldClock entity directly, allowing Home Assistant to handle daylight-saving transitions automatically.

This makes it possible to display multiple cards simultaneously for locations around the world, each showing its own correct local time.

---

# 🌦 Supported Weather Providers

Sirgon Globe works with any Home Assistant weather entity.

Tested with:

- Met.no
- OpenWeatherMap
- Pirate Weather
- National Weather Service
- Environment Canada
- Any Home Assistant weather integration

---

# 🔧 Installation

## HACS

1. Open **HACS**
2. Navigate to **Frontend**
3. Search for **Sirgon Globe**
4. Click **Download**
5. Refresh your browser

---

## Manual Installation

Download

```
sirgon-globe.js
```

Copy to

```
/config/www/
```

Add as a Dashboard Resource

```
URL:
/local/sirgon-globe.js

Type:
JavaScript Module
```

---

# ⚙️ Basic Configuration

```yaml
type: custom:sirgon-globe

entity: weather.forecast_home

local_time: sensor.home_time

show_clock: true

show_details: true

forecast_type: daily
```

---

# 🚀 Advanced Example

```yaml
type: custom:sirgon-globe

entity: weather.forecast_porto

local_time: sensor.europe_lisbon

show_clock: true

show_details: true

forecast_type: daily

local_weather_station: true

local_temperature: sensor.porto_temperature

local_humidity: sensor.porto_humidity

local_pressure: sensor.porto_pressure
```

---

# 🛣 Roadmap

## Completed

- Independent WorldClock support
- Multiple weather providers
- Multiple weather sources
- Local weather stations
- Visual configuration editor
- HACS support
- Animated weather engine
- Dynamic sky rendering

---

## Planned

- Weather alerts
- Radar overlays
- Air quality enhancements
- Sunrise / Sunset timeline
- Additional translations
- Additional weather animations

---

# 🤝 Contributing

Contributions are always welcome.

Bug reports, feature requests, pull requests, translations and documentation improvements are greatly appreciated.

---

# 🙏 Acknowledgements

Sirgon Globe began as a fork of the excellent **Nimbus Weather Card**, originally created by **Max Fok**.

Nimbus provided the visual foundation and weather rendering engine that made this project possible.

Sirgon Globe continues as an independent project focused on multi-location dashboards, WorldClock integration, and globally distributed Home Assistant installations.

Special thanks to the original author for making Nimbus open source.

---

# 📄 License

This project is released under the MIT License.

See the **LICENSE** file for details.