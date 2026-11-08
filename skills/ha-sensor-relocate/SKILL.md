---
name: ha-sensor-relocate
description: Relocate a sensor device between areas. Use when the user asks to move a sensor to another room, rename a sensor device, or add a second presence sensor to a room.
---

# HA Sensor Relocate

Move a sensor device to another area, rename it consistently, and update every consumer. Proposal only: stop after the plan and wait for explicit user approval before changing anything.

## Steps

1. **Inventory.** Read the device (via entity → `device_id`): list enabled entities vs disabled diagnostics. Disabled ones move with the device and need no rename.
2. **Consumers.** Search every old `entity_id` across automations, scripts, scenes, dashboards, and groups. Record the list; it becomes the update checklist. Note automations in the emptied area that lose their last sensor.
3. **Propose.** Present: device move (`area_id`), device rename, `old entity_id` → `new entity_id` per enabled entity, per-consumer edits (multi-sensor rooms: single-sensor `occupancy.is_not_detected` → target list + `behavior: all`, see [good-practices](../../docs/good-practices.md); emptied area: downgrade dependent automations instead of pointing them at another room). Stop and wait for approval.
4. **Apply.** Move the device (`ha_set_device`), rename enabled entities (`ha_set_entity` with `new_entity_id`), update every consumer from step 2.
5. **Verify.** Search each old ID: expect zero entities and zero configs. Search each new ID: expect the entity plus every checklist consumer. Read the new states to confirm they report.

Completion: no old ID has matches, every new ID resolves and reports, every consumer from step 2 was updated or explicitly downgraded.
