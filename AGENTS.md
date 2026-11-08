# ha-playbooks

Skills and docs for Home Assistant automation.

## Environment

All live Home Assistant state (automations, entities, traces, logbook) is read and changed **only via the Home Assistant MCP**. The MCP must be connected in the agent session before any skill runs. Never parse `automations.yaml` dumps by hand when the MCP is available; never invent entity IDs or states.

If the MCP is not connected, stop and ask the user to connect it instead of guessing.

## Safety

Propose first, change later. Never create, edit, disable, or delete automations, helpers, or scripts without explicit user approval. Present the proposal and stop; apply only what the user confirms.
