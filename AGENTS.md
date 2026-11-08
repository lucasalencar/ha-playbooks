# ha-playbooks

Skills and docs for Home Assistant automation.

## Repo layout

- `docs/` is knowledge: definitions, the current organization, best practices, pitfalls. Anything an agent needs to **know** before acting lives here.
- `skills/` is action: recurring operations and processes (e.g. audit, cleanup, consolidation). Anything an agent needs to **do** lives here, and skills reference `docs/` instead of embedding knowledge.

## Environment

All live Home Assistant state (automations, entities, traces, logbook) is read and changed **only via the Home Assistant MCP**. The MCP must be connected in the agent session before any skill runs. Never parse `automations.yaml` dumps by hand when the MCP is available; never invent entity IDs or states.

If the MCP is not connected, stop and ask the user to connect it instead of guessing.

## Safety

Propose first, change later. Never create, edit, disable, or delete automations, helpers, or scripts without explicit user approval. Present the proposal and stop; apply only what the user confirms.

## Privacy

This is a public repo: never put real household data in `docs/`, `skills/`, commit messages, or PR descriptions. That includes real device models, entity IDs, person names, areas, and automation contents from the house. Always use fictitious examples (e.g. `TV Philips 55P700`, `media_player.tv_philips_55p700`) and mark them as such. Use brands you do not own so examples can't be mistaken for real devices. Real values may only appear in live MCP tool calls, never in files or history.

## Learning loop

After any HA change, improvement, or update, run a brief retrospective before the final summary: was there a reusable learning, gotcha, or process? If yes, suggest it in 1-2 lines with the target (`docs/` for knowledge, `skills/` for a repeatable process) and wait for approval. Create or update docs/skills only after the user confirms.
