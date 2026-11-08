# Organization scheme

How automations in this house are organized across the three native HA axes.
Read this before creating an automation or assigning area/category/labels, so
new automations land in the existing scheme instead of inventing a new one.

## The three axes (one role each)

| Axis | Role | Cardinality | Rule |
|------|------|-------------|------|
| Area | **Where** the automation acts | 0–1 per automation | Set the area whose devices it controls. Casa-wide routines (wake-up, good-night) stay area-less. |
| Category (`automation` scope) | **Functional group** | Exactly 1 per automation | Pick from the table below. Do not create a new category to mirror a label (see [No duplication](#no-duplication)). |
| Label | **Cross-cutting trait** | 0–N per automation | Apply every label whose definition matches, regardless of category/area. |

Assigning area/category/labels is registry metadata only: it never renames
anything and breaks no consumers (dashboards, scripts, other automations).
Renames and restructures are different operations — those follow the
`ha-automation-audit` skill's [good-practices](./good-practices.md)
and the safe-refactoring workflow.

## Categories (scope: `automation`)

Resolve IDs at apply time with `ha_config_get_category("automation")`; names
are the stable contract, IDs are not.

| Category | Covers | Examples in this house |
|----------|--------|------------------------|
| Iluminação | Lights, switches, ambience scenes | Room light switches, dusk lights, "aconchegante"/cinema scenes |
| Rotina | Daily routines and schedules | Bom dia, Boa noite, Horário de dormir, sit-stand desk reminders |
| Presença | Arrival/departure detection and welcome/leave sequences | Chegada/saída do Lucas, Cheguei em casa, Saindo de casa |
| Gatos | Everything about the cats | Feeders (normal and travel), toxic-plant checks |
| Segurança | Entry-point monitoring | Front-door left-open / door-state notifications |

## Labels

| Label | Apply when |
|-------|------------|
| `notificações` | The automation sends notifications to mobile devices |
| `viagem` | The automation only applies (or changes behavior) while traveling / away for a long time |
| `gatos` | The automation concerns the cats |

## Areas

Current areas: Sala de estar, Quarto, Escritório do Lucas, Escritório da
Jacque, Cozinha. No floors (single-story home).

- An automation driving devices in one area gets that area
  (`interruptor_do_escritorio` → Escritório do Lucas).
- Automations reacting to a person (arrival/departure) or running house-wide
  stay area-less.

## Device names

Device names are stable identity; area says where the device is. Never put
the area in the device name — it goes stale on every move (a TV named after
one room ends up in another).

Pattern: `TV <Marca> <Polegadas><Modelo>` (fictitious examples, brands not
owned here: `TV Philips 55P700`, `TV Sony 43X700`). Same brand + size: disambiguate by model
first, then `01`/`02` by serial. Moving rooms only changes the area, never
the name. `entity_id`s follow the same pattern
(`media_player.tv_philips_55p700`).

## No duplication

Never track the same concept as both a label and a category. `Viagem` used to
exist as a category while `viagem` was also a label; the empty category was
deleted and travel is a label only. If a new dimension appears, decide
upfront: one bucket (category) or a tag (label) — not both.
