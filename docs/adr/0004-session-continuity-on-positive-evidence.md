---
status: accepted
---

# Session Continuity is classified on positive evidence only

The current single-player build exposes no universal Operational Session
identifier on the wire, and the Communication Port can go silent or reconnect
while the game keeps running. Session Continuity is Same Session when the same
established Communication Port connection resumes after silence, or when after
a reconnect the same Tower! Simulator 3 process instance, compatible Ready
context and a current `AIRPLANES` record joined to the same game-assigned
strip-content instance from the Recovery Snapshot are observed, in both cases
without a positive session-end fact; it is Different Session when the process
instance is replaced or an explicit menu, session-end or other admitted
replacement fact appears, and such a fact always wins; every other case,
including conflicting admitted evidence, is Ambiguous, and the user cannot
override the classification. The evidence is in
[Prototype Operational Session continuity across Communication Port disruption](https://github.com/CrankyAnt/TowerGlance/issues/29#issuecomment-5245600686);
host restart follows the same rule under
[Define the browser state and reconnect contract](https://github.com/CrankyAnt/TowerGlance/issues/11#issuecomment-5594812498).

## Considered options

- **Time-outs or elapsed time:** a complete five-second communication silence
  occurred inside one uninterrupted session, so silence proves nothing.
- **Similarity evidence:** a deliberately similar restart at the same airport
  repeated the airport, player context and listener address and reused an
  `AIRPLANES` candidate, and callsigns and indices may repeat across sessions
  ([Establish v1-relevant Communication Port data semantics](https://github.com/CrankyAnt/TowerGlance/issues/27#issuecomment-5234586453)).

## Consequences

- A Recovery Snapshot is reconciled only for Same Session. Different Session
  rejects it, and Ambiguous starts a separate live context in which the
  snapshot stays unlinked and read-only.
- The 30-minute Recovery Window bounds restoration after a host restart; it is
  a recovery policy, not evidence of a new session.
