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
