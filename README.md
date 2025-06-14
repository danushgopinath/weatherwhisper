# WeatherWhisper 🌦️

**WeatherWhisper** is a lightning‑fast, cross‑platform command‑line interface (CLI) that delivers real‑time weather conditions and five‑day forecasts without ever opening a browser. It relies on the free **OpenWeatherMap** API, caches results locally to respect rate‑limits, and supports both metric and imperial units.

---

## 📑 Table of Contents

1. [Features](#features)
2. [Quick Demo](#quick-demo)
3. [Installation](#installation)
4. [Get an API Key](#get-an-api-key)
5. [Usage](#usage)

   * [Command Reference](#command-reference)
   * [Common Flags](#common-flags)
   * [Output Views](#output-views)
6. [Configuration](#configuration)
7. [Caching & Rate Limits](#caching--rate-limits)
8. [Examples](#examples)
9. [Development](#development)
10. [Roadmap](#roadmap)
11. [Contributing](#contributing)
12. [License](#license)

---

## Features

* **Current conditions & five‑day / three‑hour forecasts** for any city or `lat,lon` pair.
* Choose **metric** or **imperial** units with `-u metric|imperial`.
* Colour‑coded output that visually maps temperature and conditions (toggle with `--no-color`).
* Optional ASCII weather icons for quick visual context (toggle with `--no-ascii`).
* Multiple **output views**: `brief`, `detailed`, `json`, and `forecast`.
* Save unlimited **favourites** for one‑command look‑ups.
* Transparent on‑disk **caching** to reduce API calls and speed up responses.
* Extra data points: sunrise / sunset, wind, humidity, pressure, visibility, cloud cover.
* Designed for future **plug‑in providers** (AccuWeather, WeatherKit, etc.).

---

## Quick Demo

Below is a brief text‑only illustration of what you can expect. Replace `«location»` with a real city or coordinates:

```bash
$ weatherwhisper current "«location»"
London • 2025‑06‑07 22:14
Temp      18 °C  (feels 17 °C)
Condition Clear
Wind      4 m/s  NW
Humidity  60 %
Sunset    21:08
```

```bash
$ weatherwhisper forecast "«location»" -u imperial -f forecast
Sun  • 73 °F / 55 °F  Partly cloudy
Mon  • 70 °F / 54 °F  Light rain
Tue  • 69 °F / 50 °F  Clear
Wed  • 71 °F / 51 °F  Clear
Thu  • 74 °F / 57 °F  Few clouds
```

---

## Installation

```bash
pip install --upgrade weatherwhisper   # Requires Python ≥ 3.7
```

On first launch, WeatherWhisper creates a config file at `~/.weatherwhisper/config.ini`.

### Alternate Install (development mode)

```bash
git clone https://github.com/danushgopinath/weatherwhisper.git
cd weatherwhisper
python -m venv venv && source venv/bin/activate
pip install -e .
```

---

## Get an API Key

1. Sign up at [https://openweathermap.org/api](https://openweathermap.org/api).
2. Copy your **32‑character** API key.
3. One‑time configuration:

```bash
weatherwhisper config --api-key YOUR_KEY
```

The key is stored in `config.ini` so you only need to set it once.

---

## Usage

### Command Reference

| Command               | Purpose                        | Accepted locations           |                                  |   |
| --------------------- | ------------------------------ | ---------------------------- | -------------------------------- | - |
| `current <location>`  | Current weather conditions     | City name or `lat,lon`       |                                  |   |
| `forecast <location>` | Five‑day / three‑hour forecast | Same as above                |                                  |   |
| \`favorites add       | list                           | remove\`                     | Manage favourite locations       | – |
| \`config show         | set\`                          | View or update configuration | Any flag can be saved as default |   |

Run `weatherwhisper --help` or `<command> --help` for a complete list of flags.

### Common Flags

* `-u, --units metric|imperial`  — Choose °C / m or °F / mi.
* `-f, --format brief|detailed|json|forecast` — Select output view.
* `--no-color` — Disable ANSI colours (useful in logs and scripts).
* `--no-ascii` — Hide ASCII weather icons.
* `--cache <minutes>` — Override the default cache time‑to‑live.

### Output Views

| View       | Description                                         |
| ---------- | --------------------------------------------------- |
| `brief`    | One‑line summary per location.                      |
| `json`     | Raw JSON suitable for scripting.                    |
| `forecast` | Five‑day grid with daily highs/lows and conditions. |

---

## Configuration

WeatherWhisper stores preferences in `~/.weatherwhisper/config.ini`:

```ini
[general]
api_key = YOUR_KEY_HERE
units = metric          # metric or imperial
color = true            # true or false
ascii = true            # true or false
cache_minutes = 15      # default cache duration
```

You may edit the file directly *or* manipulate it via the CLI:

```bash
weatherwhisper config set --units imperial --no-color
weatherwhisper config show
```

---

## Caching & Rate Limits

* All API responses are cached on disk for **15 minutes** by default.
* Cache entries are keyed by **endpoint + location + units**.
* Pass `--cache 0` to bypass the cache and request live data.
* The default settings keep most users well below the free‑tier limit of **60 calls per minute**.

---

## Examples

```bash
# Show current weather for Paris in the default view.
weatherwhisper current "Paris"

# Retrieve JSON output for London and pipe the temperature to jq.
weatherwhisper current "51.5074,-0.1278" -f json | jq .temp

# Display a colour‑free forecast grid for Tokyo.
weatherwhisper forecast "Tokyo" --no-ascii --no-color

# Use your first favourite automatically (if favourites exist).
weatherwhisper current
```

---

## Development

```
weatherwhisper/
 ├─ images/              # PNG assets (optional; not required for CLI)
 ├─ __init__.py
 ├─ api.py               # API request & response logic
 ├─ cli.py               # Main CLI entry point
 ├─ config.py            # Config file parsing & CLI setters
 ├─ formatter.py         # Output colour & ASCII rendering
LICENSE
pyproject.toml
setup.py
README.md
```

\### Set Up Dev Environment

```bash
git clone https://github.com/danushgopinath/weatherwhisper.git
cd weatherwhisper
python -m venv venv && source venv/bin/activate
pip install -e ".[dev]"      # pytest, ruff, black, pre‑commit
pre-commit install
```

Run from source:

```bash
python -m weatherwhisper current "London"
```

Execute test suite:

```bash
pytest -q
```

---

## Roadmap

* **Plug‑in system**: drop‑in providers (AccuWeather, WeatherKit, & more).
* **Historical weather**: optional add‑on for paid API tiers.
* **Severe weather alerts**: push notifications for advisories.
* **Auto‑detect location**: IP or GPS‑based geolocation.
* **GUI wrapper**: optional desktop interface (Tkinter / Electron).

Track progress on the [project board](https://github.com/danushgopinath/weatherwhisper/projects).

---

## Contributing

1. **Fork**, clone, and create a feature branch.
2. Run `pre-commit install` (formats code and runs tests on commit).
3. Write or update **tests** for your changes.
4. Open a **Pull Request** with a clear description of the feature or fix.

All feedback and PRs are welcome—check open [issues](https://github.com/danushgopinath/weatherwhisper/issues) or start a new one.

---

## License

MIT © 2025 Danush Gopinath

---

*Made with ☕ and a love of clouds.*
