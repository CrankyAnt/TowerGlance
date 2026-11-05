---
status: accepted
---

# The Traffic Schedule Source is identified from the game's own selector configuration

The Communication Port returns an empty `SCHEDULE` in the current build and
names no selected database or main schedule file, so planned traffic comes
from local game files. In the current supported single-player build,
TowerGlance admits a Traffic Schedule Source only from one stable, coherent
observation of the same Ready Operational Session: the game's per-airport
database selector index, resolved against the installed profiles in the
game-equivalent order and corroborated by profile-backed live configuration,
with the Quick Play or Career configuration selecting `schedule.csv` or
`schedule_career.csv` and each applicable file resolved through the game's own
fallback order. The evidence is in
[Prototype authoritative Traffic Schedule Source identification](https://github.com/CrankyAnt/TowerGlance/issues/30#issuecomment-5247468431)
and [Establish v1-relevant Communication Port data semantics](https://github.com/CrankyAnt/TowerGlance/issues/27#issuecomment-5234586453).

## Considered options

- **The profile named `default`:** selector index zero pointed to a profile
  with another name, so neither the name nor alphabetical order selects it.
- **Correlation of planned rows with live traffic:** the schedule must be known
  before planned flights become live, and a match is an association, not proof
  of selection.
- **`Player.log` or save files as authority:** no release method can prove that
  an existing save file is the one loaded, and `Player.log` carries no general
  live-state authority.

## Consequences

- The accepted source is read into an immutable snapshot scoped to the
  Operational Session. A change detected during acquisition invalidates that
  attempt, and a later local file change does not replace the accepted source
  within that session.
- `ga.csv` and `local_traffic_cfg.csv`, when applicable, are resolved the same
  way and degrade independently of the main schedule; disabled local or VFR traffic is
  `not configured`, not `unavailable`.
- Only for a loaded save whose selector or mode state cannot be established
  may the current `GAME START` configuration in `Player.log` supply the
  database and the Quick Play or Career branch, when it is positively bound to
  the same Ready Operational Session.
- A new game build or changed selector behaviour starts `unverified`, and a
  selector or resolver failure makes only the dependent schedule layer
  `unavailable`.
