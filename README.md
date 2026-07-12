# Dan's Next Meeting

A TRMNL private plugin that shows your current/next calendar event (or room
booking) as an availability display on an e-ink screen.

It reads events from a permissioned **Google Calendar** plugin instance
(`plugin_merge` strategy) and renders an "In a meeting / Available" hero plus
Now/Next detail. A `Mode` setting switches the copy between calendar events and
room bookings.

## Layouts

Markup for each TRMNL size lives in `src/`:

| File | TRMNL size |
| --- | --- |
| `src/full.liquid` | full |
| `src/half_horizontal.liquid` | half horizontal |
| `src/half_vertical.liquid` | half vertical |
| `src/quadrant.liquid` | quadrant |
| `src/shared.liquid` | shared (runs before every size; computes `current_event`, `next_event`, `is_busy`, labels, etc.) |

Plugin configuration (name, strategy, framework version, custom fields) is in
`src/settings.yml`.

## Data sources

`shared.liquid` references two other plugin instances by keyname:

- `calendar_source` — the selected Google Calendar plugin instance (chosen via the `Calendar Data Source` custom field).
- `tempest_weather_station_347799` — a Tempest weather station instance used for `max_temp`.

## Setup

1. Connect your calendar to the native Google Calendar plugin, select the
   desired calendars, and add it to your playlist. Use the Agenda view and
   disable "Include past events".
2. Hide the calendar plugin.
3. Fork this plugin and select the correct Calendar Data Source in this
   plugin's settings.

> Requires TRMNL Developer Edition or BYOD.

## Local development

These files follow the [`trmnlp`](https://github.com/usetrmnl/trmnlp) layout, so
you can preview all four sizes in the browser with live reload.

Requires **Ruby ≥ 3.4** (the CLI is shipped as the `trmnl_preview` gem):

```sh
# macOS system Ruby is too old; install a modern Ruby first, e.g.:
brew install ruby
# then make sure it's on your PATH (Homebrew keg-only):
export PATH="$(brew --prefix ruby)/bin:$PATH"

gem install trmnl_preview
trmnlp serve            # http://localhost:4567
```

Local mock data lives in `.trmnlp.yml` (gitignored). Because this plugin uses the
`plugin_merge` strategy, its data sources (`calendar_source`,
`tempest_weather_station_347799`) don't exist locally — `.trmnlp.yml` fakes them
so the preview renders a populated screen. The current/next logic is
time-relative, so adjust the sample event dates/times to "now" to see the
"In a meeting" vs "Available" states.
