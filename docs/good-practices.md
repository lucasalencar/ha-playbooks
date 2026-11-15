# Good practices

Reference for `ha-automation-audit` merge/remove decisions.

## Merge signals

- Same trigger, different action per room/time: merge with `trigger_id` + `choose`.
- Same condition repeated in 3+ automations: extract to helper (`group`, `schedule`, `input_boolean`, `template` binary sensor).
- Scene-like bursts of `light.turn_on` / `switch.turn_on`: prefer `scene` or `script`.

## Dead signals

- Disabled for 30+ days with no linked request to keep.
- Trigger entity no longer exists (`unavailable` / `unknown` permanently).
- No trace in 90 days and trigger is not seasonal (heating, Christmas). Seasonal goes to `keep` with a note, never `remove`.

## Fragility to flag

- `time_pattern` polling faster than 5 min where a state/event trigger fits.
- Missing `mode`: default `single` silently drops overlaps; use `restart` for motion/buttons, `queued` for sequences that must not overlap, `parallel` only with a `max` bound.
- Missing `alias` or `description`: blocks future audits.
- Broad state triggers with no `for:` / `from:` / `to:`: fires on every attribute write.

## Multi-sensor presence

- Room with N occupancy sensors: `occupancy.is_not_detected` with `target: [all sensors]` + `behavior: all` (empty only when every sensor is clear). `behavior: any` here means empty when any single sensor clears — wrong for large rooms.
- Occupied is the mirror: `occupancy.is_detected` with `behavior: any`.
- Apply the same sensor list everywhere the room's presence is checked (arrival, good-night, away detection, light-on conditions); one stale single-sensor check defeats the second sensor.
- Whole-house presence = person trackers OR any occupancy sensor (`condition: or`); put the empty-house outcome in `default` so `unknown` trackers fail toward it, not toward lighting the empty house.

## Trigger guard per branch

Every branch of a multi-trigger automation needs a `condition: trigger` with its trigger ID. Without it, the second trigger falls through into the first branch (e.g. a 21:00 time trigger re-running the sunset scene).

## Fixed times for circadian regularity

Prefer fixed clock times over sun offsets for steps meant to entrain the sleep cycle (e.g. a 21:00 warm-down): fixed times reinforce the rhythm; sun offsets drift daily. Keep sun triggers only where the astronomical event itself matters (dusk lighting).

## Confirm actuator semantics (when in doubt)

When unsure what a command physically does on the device, check before designing triggers and schedules around it (e.g. `turn_on` on a smart plug energizes the outlet — it doesn't start the appliance). No check needed for well-known actuators.

## Light commands (no-flash)

- Color + brightness + `transition` always in the same `light.turn_on` — never a bare `turn_on` followed by an adjustment (that's what causes the white flash at the last state).
- Order: turn off first what stays off, turn on what stays on already with final parameters.
- `transition` in seconds for changes with someone in the room; scenes apply instantly.
- `color_temp_kelvin`, never `color_temp` (mireds removed in 2026.3).

## Fail-safe for all-off

- A destructive action's condition must fail toward NOT acting when the sensor/entity is `unavailable` (native conditions already do this — don't bypass with templates).
- Cross-signals: TV on = room in use.
- Vacancy with `for:` (15 min for the whole house) instead of instant "not detected" — sensors don't see someone sitting still.
