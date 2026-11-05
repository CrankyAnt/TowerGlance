---
status: accepted
---

# Browsers are views of host-owned state and never queue or replay commands

Several same-device browser windows can control one Operational Session, and a
browser can reload, disconnect or outlive a host restart. The TowerGlance Host
owns the shared current application state and orders and validates every
command against current evidence; a browser receives a complete coherent
baseline before interaction resumes, keeps a disconnected view visibly
last-known and read-only, and resynchronizes to the host on reconnect instead
of queuing or replaying user actions. The evidence is in
[Define the browser state and reconnect contract](https://github.com/CrankyAnt/TowerGlance/issues/11#issuecomment-5594812498);
the host and its transport are recorded in
[ADR 0001](0001-host-storage-packaging-architecture.md).

## Considered options

- **Commands queued in the browser for later delivery:** a queued strip move
  could act on an obsolete view and cause a game write that the current state
  no longer justifies.
- **Operational state kept in the browser:** the host has to keep following the
  session and Automatic Stripboard Mode while no browser is open, and all
  windows share one state.

## Consequences

- A lost response, a duplicate delivery or a reconnect never causes a second
  game write; across a host restart an unproven outcome gives no permission to
  resubmit a command.
- Navigation, zoom and filters stay local to each window.
