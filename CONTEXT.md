# TowerGlance

TowerGlance presents the local operational picture of a Tower! Simulator 3 session in an independent local webview and may project explicitly selected stripboard placement back into that session.

## Language

**Operational Session**:
The currently active Tower! Simulator 3 run at one airport whose observed and derived operational state TowerGlance presents. A TowerGlance instance follows at most one live Operational Session.
_Avoid_: Browser session, user session

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
A TowerGlance-owned local copy of previously reconstructed state for an identifiable Operational Session, used to restore the experience quickly. Its age must remain visible to TowerGlance, and newer game-derived information always supersedes it.
_Avoid_: Browser cache, authoritative game data

**Stripboard Mode**:
The user-selected policy by which TowerGlance advances Game-backed Strip Positions and maintains order across its entire external stripboard. A stripboard uses either Automatic Stripboard Mode or Manual Stripboard Mode.
_Avoid_: Automation setting

**Game-backed Strip Position**:
The operational block assigned to a strip and recorded in Tower! Simulator 3 so TowerGlance and the in-game board can present the same placement. It excludes the strip's order within that block.
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

**Schedule Horizon**:
The future interval for which Tower! Simulator 3 has made traffic information available to TowerGlance. It may vary by session and game settings such as schedule preload and is not a fixed duration promised by TowerGlance.
_Avoid_: Forecast window

**Source Health**:
TowerGlance's assessment of the availability, freshness, and successful interpretation of one local Tower! Simulator 3 information source. It does not imply that the entire Operational Session is healthy.
_Avoid_: Connection status, overall health

**Local Access**:
The default access boundary in which TowerGlance accepts browser clients only from the computer running TowerGlance.
_Avoid_: Offline mode
