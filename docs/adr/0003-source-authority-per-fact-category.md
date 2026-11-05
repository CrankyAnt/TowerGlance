---
status: accepted
---

# Source authority is assigned per fact category and fails closed

Tower! Simulator 3 exposes overlapping local sources of differing reliability:
the Communication Port, static game files and `Player.log`. Each Normalized
Fact category has one explicit Source Authority Rule that names the source or
source combination allowed to establish it, with no global best source and no
latest-wins merge; when admitted sources disagree and no explicit precedence
rule of that category resolves them, the fact is ambiguous and only the
dependent automatic action or layer pauses. When no safe authoritative source
exists, the dependent capability fails closed instead of fabricating facts,
automating or writing, and a Recovery Snapshot is TowerGlance-owned stale
state that is never authority for current game facts. The evidence is in
[Define Operational Session identity, source authority, and recovery](https://github.com/CrankyAnt/TowerGlance/issues/7#issuecomment-5244333107)
and [Prototype a minimal read-only TS3 session capture](https://github.com/CrankyAnt/TowerGlance/issues/4#issuecomment-5156717657).

## Considered options

- **One best source, or the latest observation wins:** simpler, but authority
  differs per fact; in the current single-player build `AIRPLANES` carries
  physical state, `STRIPS` game-maintained strip content and static files the
  geometry, and receipt order creates no precedence between independent
  sources.
- **`Player.log` as a live state source:** it establishes only that a
  diagnostic line was emitted, without stable identifiers, ordering or
  lifecycle markers.
- **Best-effort operation on partial or cached facts:** stale or incomplete
  data would be presented as current operational truth.

## Consequences

- A new or changed source and a newly required fact begin `unverified` and
  need their own Source Authority Rule; they never inherit authority from a
  related one.
- Absence counts only through an explicit removal signal or an observation
  proven complete for that fact category.
- Spoken text, recognized text, parsed intent, game-applied state and
  aircraft-executed state stay separate fact categories.
- Multiplayer availability, worker-route fan-out and stability across game
  builds remain unverified and are not implied by these rules.
