# Dan's Next Meeting

A TRMNL private plugin that shows your current/next calendar event (or room
booking) as an availability display on an e-ink screen.

It reads events from a permissioned **Google Calendar** plugin instance
(`plugin_merge` strategy) and renders an "In a meeting / Available" hero plus
Now/Next detail. On the **full** view it also shows a compact **Tempest weather
strip** across the top. A `Mode` setting switches the copy between calendar
events and room bookings.

The full view is built for the **TRMNL X** display (1040×780 logical).

## Layouts

Markup for each TRMNL size lives in `src/`:

| File | TRMNL size | Notes |
| --- | --- | --- |
| `src/full.liquid` | full | Weather strip + availability + Now/Next, laid out for TRMNL X. This is the primary, actively-designed view. |
| `src/half_horizontal.liquid` | half horizontal | Availability + Now/Next only (no weather). |
| `src/half_vertical.liquid` | half vertical | Availability + Now/Next only (no weather). |
| `src/quadrant.liquid` | quadrant | Availability + next event only (no weather). |
| `src/shared.liquid` | shared | Runs before every size; computes `current_event`, `next_event`, `is_busy`, mode labels, and the `wx_*` weather variables. |

Plugin configuration (name, strategy, framework version, custom fields) is in
`src/settings.yml`.

## Full view layout (TRMNL X)

- **Weather strip** — pinned flush to the top (`layout--top`), with a divider rule beneath it.
- **Availability hero** — "In a meeting / Available" grows to fill the remaining space and is vertically centered.
- **Now / Next** — the schedule detail is anchored at the bottom, above the title bar.
- **Title bar** — given a taller custom height (72px) so the avatar image renders larger.

### Weather strip

Two lines, sourced from the Tempest instance and computed in `shared.liquid`:

- **Now:** condition icon + `right_now_conditions` · 🌡 `temperature`° feels `feels_like`° · 💧 `humidity`%
- **Today:** condition icon + `today_conditions` · 🌡/☀ `maxtemp`° `mintemp`° · 💧 `precip.probability`% rain

Icons use TRMNL's hosted weather set (`trmnl.com/images/plugins/weather/…`):
`wi-thermometer`, `wi-humidity`, `wi-raindrop`, and `wi-hot` — the today line
swaps the thermometer for `wi-hot` when today's high is above **85°**. The two
condition glyphs come from Tempest's own `weather_image` / `today_weather_image`.

## Data sources

`shared.liquid` references two other plugin instances by keyname (via the
`plugin_merge` strategy):

- `calendar_source` — the selected Google Calendar plugin instance (chosen via the `Calendar Data Source` custom field). Provides `events` (the schedule).
- `tempest_weather_station_347799` — a Tempest weather station instance. Provides current + today conditions, temperature, feels-like, humidity, high/low, rain probability, and condition icon images for the full-view weather strip.

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
you can preview the sizes in the browser with live reload.

Requires **Ruby ≥ 3.4** (the CLI is shipped as the `trmnl_preview` gem):

```sh
# macOS system Ruby is too old; install a modern Ruby first, e.g.:
brew install ruby
# then put both Ruby and its gem executables on your PATH (Homebrew keg-only):
export PATH="$(brew --prefix ruby)/bin:$(gem env gemdir)/bin:$PATH"

gem install trmnl_preview
trmnlp serve            # http://localhost:4567
```

Local mock data lives in `.trmnlp.yml` (gitignored). Because this plugin uses the
`plugin_merge` strategy, its data sources (`calendar_source`,
`tempest_weather_station_347799`) don't exist locally — `.trmnlp.yml` fakes them
so the preview renders a populated screen. The current/next logic is
time-relative, so adjust the sample event dates/times to "now" to see the
"In a meeting" vs "Available" states.

To preview the TRMNL X full view specifically, select **TRMNL X** in the
trmnlp model picker (renders at 1040×780 logical).
