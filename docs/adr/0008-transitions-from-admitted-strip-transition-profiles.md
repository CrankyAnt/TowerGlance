---
status: accepted
---

# Automatic strip transitions come only from admitted Strip Transition Profiles

The active game-owned stripboard profile supplies the block topology, the
direction-specific start positions and the `Next` edges, but not the
operational trigger for traversing an edge, and the installed profiles form
five materially different structural classes. Automatic Stripboard Mode moves a
strip only through an admitted Strip Transition Profile that maps positive
authoritative Operational Facts for one traffic and scenario class onto the
active graph; in the current build only the Regular IFR mapping for the
three-edge structural class is admitted, on Quick Play evidence, and every
other profile class, traffic variant and scenario leaves its automatic
progression unadmitted until its own verification succeeds. The evidence is in
[Prototype deterministic automatic strip transitions](https://github.com/CrankyAnt/TowerGlance/issues/9#issuecomment-5285841804)
and [Define the stripboard domain and mode contract](https://github.com/CrankyAnt/TowerGlance/issues/10#issuecomment-5593156288).

## Considered options

- **Transitions inferred from block header text:** free-form header wording is
  no stable semantic contract across the game-owned stripboard profiles.
- **Transitions from command phrases or a single state field:** an early
  runway assignment did not establish effective taxi, an accepted taxi
  instruction did not establish runway vacation, and a pre-bundled handoff did
  not occur until the later handoff fact became positive.
- **One mapping for all traffic classes and scenarios:** General Aviation, VFR,
  Local Flight, Career Challenge equivalence and non-nominal paths such as a
  go-around do not inherit the Regular IFR mapping without evidence.

## Consequences

- Where no admitted profile applies, Manual Stripboard Mode can stay available
  for strips whose game-derived identity and content remain reliable
  ([Decide whether supplemental local data is needed](https://github.com/CrankyAnt/TowerGlance/issues/6#issuecomment-5156535468)).
- Missing, ambiguous, stale, unavailable or unverified evidence causes no
  transition and no write; the strip stays at its observed position.
