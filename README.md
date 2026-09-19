# Roborock, with the map refresh fix

Home Assistant's built-in `roborock` integration at **2026.9.3**, with one
change. Installed as a custom component it takes the place of the built-in
one and keeps the same config entry, devices and entities.

## What it fixes

While a vacuum is cleaning, the built-in integration fetches and parses its
map every 30 seconds, twice over (`discover_home()` re-parses every cached
map on each call, then `home.refresh()` parses the current one). Each parse
blocks Home Assistant's event loop for a few hundred milliseconds, whether
or not anything shows the map. Measured on one install: two stalls of about
0.33 s every 31 s for as long as a vacuum was cleaning (or stuck mid-clean).

Here:

- `discover_home()` is no longer called on every map update; `refresh()`
  already runs discovery until it has completed.
- The timed refresh only runs while an enabled entity shows live map
  content: a map image, or the *Current room* sensor. Disable those and a
  cleaning vacuum costs nothing. A change of vacuum state still refreshes
  once, so room names stay current.

Three files differ from core: `coordinator.py`, `image.py`, `sensor.py`.
Source branch with tests: `roborock-map-refresh-2026.9.3` of
[davidcoulson/core](https://github.com/davidcoulson/core/tree/roborock-map-refresh-2026.9.3).

## Install

HACS → ⋮ → Custom repositories → add this repository as an **Integration**,
download **Roborock (map refresh fix)**, restart Home Assistant. Then
disable the map image entities and the *Current room* sensors you do not
use.

To go back, remove it in HACS and restart: the built-in integration takes
over again.

## Upgrading Home Assistant

This copy is frozen at 2026.9.3 and pins `python-roborock==7.4.2`. After a
Home Assistant upgrade, update this component to a release matching the new
version, or remove it if the fix has landed upstream.

## Licence

Home Assistant is licensed under Apache 2.0 (see `LICENSE.md`); this is a
modified copy of its `roborock` component. Not affiliated with or endorsed by
the Home Assistant project.
