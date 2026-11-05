---
status: accepted
---

# The game records strip placement; TowerGlance writes only the Game-backed Strip Position

The In-game Strip on the Tower! Simulator 3 board stays the game's own, while
Automatic Stripboard Mode and TowerGlance-side moves have to place strips. Every
TowerGlance Strip uses the active game-owned block layout and the Game-backed
Strip Position as the one shared block-placement record; TowerGlance writes to
the game only that position, through the strip-position route verified in the
current single-player build, when the user, an admitted Strip Transition
Profile ([ADR 0008](0008-transitions-from-admitted-strip-transition-profiles.md))
or a Manual Stripboard Update selects it, and owns only the order
within a block. All other game state stays read-only, and neither a write
acknowledgement nor a strip position is lifecycle authority, which rests on
observed game state. The evidence is in
[Establish v1-relevant Communication Port data semantics](https://github.com/CrankyAnt/TowerGlance/issues/27#issuecomment-5234586453),
[Prototype deterministic automatic strip transitions](https://github.com/CrankyAnt/TowerGlance/issues/9#issuecomment-5285841804)
and [Define the stripboard domain and mode contract](https://github.com/CrankyAnt/TowerGlance/issues/10#issuecomment-5593156288).

## Considered options

- **Strictly read-only presentation:** no game mutation at all, but then the
  game and TowerGlance would keep two placement records for one strip instead
  of one shared Game-backed Strip Position.
- **A block position that exists only in TowerGlance:** no write, with the same
  second placement record beside the game's.
- **A generic command route into the game:** every further kind of write would
  need its own verified safety boundary; the host exposes no generic command
  API, as [ADR 0001](0001-host-storage-packaging-architecture.md) records.

## Consequences

- A position change converges only after acknowledgement and exact `STRIPS`
  readback. A differing readback stays the displayed game position and
  surfaces a reconciliation failure, without a blind second write.
- Reordering within a block never writes to the game and claims no physical
  aircraft sequence.
- A new game build starts the strip-position route `unverified`, and every
  further kind of game write needs its own verification before it is admitted.
