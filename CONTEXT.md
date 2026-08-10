# TowerGlance

TowerGlance presents the local operational picture of a Tower! Simulator 3 session in an independent local webview and may project explicitly selected stripboard placement back into that session.

## Language

**Operational Session**:
One continuous Tower! Simulator 3 run at one airport whose observed and derived operational state TowerGlance presents; a TowerGlance instance follows at most one such live run. Returning to the game menu ends it, and a later run is a new Operational Session rather than a continuation.
_Avoid_: Browser session, user session

**Ready Operational Session**:
An Operational Session for which game-derived evidence confirms an active run and its airport. Individual TowerGlance capabilities may still be unavailable according to their Source Health.
_Avoid_: Fully healthy session, all sources ready

**Quick Play Session**:
An Operational Session started through Tower! Simulator 3 Quick Play with a selected locally available schedule database and time window.
_Avoid_: Scheduled Session, regular session

**Career Challenge**:
An Operational Session started through Tower! Simulator 3 Career from a challenge-defined time window, traffic set, and optional operating conditions or restrictions.
_Avoid_: Career session, career schedule

**Official Airport**:
An airport package supplied with Tower! Simulator 3 or through a locally installed official airport DLC and eligible for capability-specific verification against TowerGlance's validated official-airport standard.
_Avoid_: Supported airport, known airport

**Recovery Snapshot**:
A TowerGlance-owned local copy of the last confirmed state for an identifiable Operational Session, shown only while that session's continuity remains possible during a temporary source loss. It remains stale with visible age and uncertainty and cannot drive automation or writes; confirmed session end discards it, and only newer authoritative information from the proven same session supersedes it.
_Avoid_: Browser cache, authoritative game data

**Stripboard Mode**:
The user-selected policy by which TowerGlance advances Game-backed Strip Positions and maintains order across its entire external stripboard. A stripboard uses either Automatic Stripboard Mode or Manual Stripboard Mode.
_Avoid_: Automation setting

**Game-backed Strip Position**:
The current operational block recorded for a strip in Tower! Simulator 3 so TowerGlance and the in-game board can present the same placement. It records board placement, not lifecycle state, operational correctness, or the strip's order within that block.
_Avoid_: TowerGlance-only position, strip order

**Automatic Stripboard Mode**:
A Stripboard Mode in which TowerGlance derives when strips should move between operational blocks and records their Game-backed Strip Positions. The user may correct a position or its TowerGlance order without leaving this mode.
_Avoid_: Fully automatic board

**Manual Stripboard Mode**:
A Stripboard Mode in which the user controls Game-backed Strip Positions and strip order and TowerGlance does not automatically move existing strips between operational blocks.
_Avoid_: Manual override

**Manual Correction**:
A user-directed change to one Game-backed Strip Position or to its TowerGlance order while the stripboard remains in Automatic Stripboard Mode. Its precedence and release rules are part of the automatic-board policy rather than a separate Stripboard Mode.
_Avoid_: Manual mode, game-board change

**ADIRS**:
The Tower! Simulator 3 term observed in game-owned local data for an airport-surface display. TowerGlance adopts ADIRS for its corresponding live view of an airport layout and the aircraft presented on it.
_Avoid_: Airport Map

**Traffic Schedule**:
The forward-looking and current view of planned and active traffic, including timing, delay, origin, destination, aircraft type, and occupancy information when the available game data supports them.
_Avoid_: Flight update list

**Traffic Schedule Source**:
The game-owned local database profile and schedule-file set whose verified selection supplies the planned traffic for one Operational Session. Until a Source Authority Rule proves that selection, candidate-file discovery, a default-profile assumption, session mode, or partial live-traffic correlation remains unverified and cannot establish Schedule Horizon authority.
_Avoid_: Session mode, assumed default schedule, best-matching schedule

**Schedule Horizon**:
The future interval for which Tower! Simulator 3 has made traffic information available to TowerGlance. It may vary by session and game settings such as schedule preload and is not a fixed duration promised by TowerGlance.
_Avoid_: Forecast window

**Source Health**:
TowerGlance's assessment of the availability, continuity, freshness, and successful interpretation of one local Tower! Simulator 3 information source. A successful current observation is fresh even when its values are unchanged; Source Health does not imply that the entire Operational Session is healthy.
_Avoid_: Connection status, overall health

**Source Authority Rule**:
The rule assigning each normalized fact category to one game-derived source or an explicit combination that together directly reports every required fact; its authority stops at those reported semantics and does not prove downstream intent, effect, or state. TowerGlance defines the rule and any precedence; new required facts or source behaviour reopen the affected rule and verification, while supplementary and recovered sources cannot become authoritative implicitly.
_Avoid_: Global source priority, best-effort merge

**Normalized Fact**:
A TowerGlance representation of one observed or derived fact that retains its value or explicit absence, source provenance, observation time or order, Operational Session context, and applied Source Authority Rule. It distinguishes explicit empty, unknown, unavailable, ambiguous, and stale or recovered states without inventing a default or assumed state.
_Avoid_: Merged value, best guess

**Derived Fact**:
A Normalized Fact produced by a verified deterministic TowerGlance rule from every required authoritative input applicable to the same Operational Session context, retaining the complete input provenance and applied rule. Its authority is limited to the semantics entailed by those inputs, and it cannot become current and certain when a required input is unknown, unavailable, ambiguous, or stale or recovered.
_Avoid_: Inferred truth, heuristic fact, upgraded evidence

**Ambiguous Fact**:
A Normalized Fact for which two admitted authority sources report conflicting values within the same applicable Operational Session context. It preserves every conflicting value and its provenance and cannot drive affected automatic behaviour unless an explicit category-specific precedence rule resolves the conflict.
_Avoid_: Unknown fact, unavailable fact, latest value wins

**Observation Order**:
The source-scoped order in which TowerGlance may compare observations when their Source Authority Rule proves a common ordering within the applicable Operational Session context. Receipt time alone creates no precedence, and observations from different sources or across a reconnect are not implicitly comparable.
_Avoid_: Arrival order, latest received wins

**Live Traffic Identity**:
The Operational Session-scoped identity by which TowerGlance correlates an observed aircraft and its strip through the verified AIRPLANES and STRIPS relationship. Callsigns, aircraft indices, strip indices, and schedule rows may participate in a verified match but do not carry identity across sessions; a schedule-to-live match is an association rather than identity.
_Avoid_: Callsign identity, persistent aircraft ID

**Authoritative Absence**:
An explicit removal signal, or an explicitly empty fact or omission from an observation whose Source Authority Rule proves completeness for the affected category. A missing record in a partial update, delta, communication gap, or observation of unverified completeness is not proof of removal or lifecycle completion and may only leave current state unknown or stale or recovered.
_Avoid_: Not seen means removed, timeout deletion

**Capability Contract**:
The downstream definition of the smallest independently useful layer or action and its required and optional Normalized Facts. A supported unit is available only while every required Source Authority Rule is satisfied; optional gaps remain explicit, while unverified and unsupported units receive no operational availability status.
_Avoid_: Whole-application health, all-or-nothing capability

**Local Access**:
The default access boundary in which TowerGlance accepts browser clients only from the computer running TowerGlance.
_Avoid_: Offline mode
