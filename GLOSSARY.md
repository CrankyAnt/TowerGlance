# TowerGlance

TowerGlance presents the local operational picture of a Tower! Simulator 3 session in local browser windows and may project explicitly selected stripboard placement back into that session.

## Language

### Host and browser

**TowerGlance Host**:
The local TowerGlance process that follows the Operational Session and keeps its state and selected Stripboard Mode independently of any browser window. A returning browser receives the current state.
_Avoid_: Browser tab, Operational Session

**Browser State**:
A browser window's projection of TowerGlance Host state, plus that window's own navigation, zoom and filters. While disconnected it stays visibly last-known and read-only; reconnection reconciles it with the host without replaying queued user actions.
_Avoid_: Authoritative game state, offline command queue

**Local Access**:
The access boundary in which TowerGlance accepts browser clients only from the computer it runs on.
_Avoid_: Offline mode

### Operational Session

**Operational Session**:
One continuous Tower! Simulator 3 run at one airport, of which TowerGlance follows at most one at a time. Returning to the game menu ends it; a later run is a new Operational Session.
_Avoid_: Browser session, user session

**Ready Operational Session**:
An Operational Session for which game-derived evidence confirms an active run and its airport. Individual capabilities may still be unavailable according to their Source Health.
_Avoid_: Fully healthy session, all sources ready

**Quick Play Session**:
An Operational Session started through Quick Play with a selected locally available schedule database and time window.
_Avoid_: Scheduled Session, regular session

**Career Challenge**:
An Operational Session started through Career from a challenge-defined time window, traffic set and optional operating conditions.
_Avoid_: Career session, career schedule

**Session Continuity**:
The classification of whether current game evidence continues the followed Operational Session (Same Session), ends it (Different Session), or leaves it Ambiguous. Only positive game evidence proves continuity; lost contact, similar airports or callsigns, and elapsed time never do.
_Avoid_: Reconnect detection, session timeout, heuristic session match

**Recovery Window**:
The 30-minute period after the TowerGlance Host's last confirmed game observation in which retained state may still be restored, if the same Operational Session is proven. After expiry TowerGlance starts fresh, which does not by itself prove that a different Operational Session began.
_Avoid_: Session lifetime, proof of session continuity

**Recovery Snapshot**:
A TowerGlance-owned local copy of the last confirmed state of an Operational Session, shown stale with its age during a temporary source loss. It never drives automation or writes, and only newer authoritative information from the proven same session supersedes it.
_Avoid_: Browser cache, authoritative game data

**Official Airport**:
An airport supplied with Tower! Simulator 3 or a locally installed official airport DLC, eligible for capability-specific verification.
_Avoid_: Supported airport, known airport

### Sources and facts

**Communication Port**:
The game-owned, loopback-only interface through which the TowerGlance Host observes an Operational Session and makes its one bounded Game-backed Strip Position write.
_Avoid_: Game API, telemetry port, external-window protocol

**Source Health**:
TowerGlance's assessment of the availability, continuity, freshness and successful interpretation of one local game source. It never implies that the whole Operational Session is healthy.
_Avoid_: Connection status, overall health

**Source Authority Rule**:
The rule that assigns each category of Normalized Fact to one game-derived source, or an explicit combination of sources, as its authority. Its authority stops at what those sources report; supplementary and recovered sources never become authoritative implicitly.
_Avoid_: Global source priority, best-effort merge

**Capability Contract**:
The definition of the smallest independently useful layer or action and the Normalized Facts it requires or may use. A capability is available only while every required Source Authority Rule is satisfied.
_Avoid_: Whole-application health, all-or-nothing capability

**Normalized Fact**:
TowerGlance's representation of one observed or derived fact, carrying its value or explicit absence, provenance, observation order, Operational Session context and applied Source Authority Rule. It never invents a default or assumed state.
_Avoid_: Merged value, best guess

**Derived Fact**:
A Normalized Fact produced by a verified deterministic rule from authoritative inputs of the same Operational Session. It cannot be current and certain while any required input is unknown, unavailable, ambiguous or stale.
_Avoid_: Inferred truth, heuristic fact, upgraded evidence

**Ambiguous Fact**:
A Normalized Fact for which two admitted authority sources report conflicting values in the same Operational Session context. It keeps every conflicting value and cannot drive automatic behaviour unless an explicit precedence rule resolves it.
_Avoid_: Unknown fact, unavailable fact, latest value wins

**Unknown Fact**:
An applicable Normalized Fact for which authoritative evidence has not yet established a value or its explicit absence.
_Avoid_: Unavailable fact, explicitly absent fact, assumed value

**Unavailable Fact**:
An applicable Normalized Fact whose configured authoritative source cannot currently be read or interpreted reliably.
_Avoid_: Unknown fact, not configured, explicitly absent fact

**Explicitly Absent Fact**:
An applicable Normalized Fact for which its authoritative source validly establishes that no value exists.
_Avoid_: Empty string default, unknown fact, unavailable fact

**Not Configured**:
The state of a Normalized Fact whose supported optional game capability is deliberately disabled or has no configured source in the Operational Session. It is not a failure: an expected source that cannot be read is unavailable.
_Avoid_: Unavailable source, missing source

**Authoritative Absence**:
An explicit removal signal, or an omission from an observation whose Source Authority Rule proves completeness. A record missing from a partial update or communication gap is never proof of removal or lifecycle end.
_Avoid_: Not seen means removed, timeout deletion

**Observation Order**:
The order in which TowerGlance may compare observations of one source, where its Source Authority Rule proves a common ordering. Receipt time alone gives no precedence, and observations from different sources or across a reconnect are not comparable.
_Avoid_: Arrival order, latest received wins

**Schedule Fact**:
A Normalized Fact about a Flight supplied by its Traffic Schedule Source. It keeps its planned value independently of later Operational Facts.
_Avoid_: Current value, fallback value

**Operational Fact**:
A Normalized Fact about a Flight supplied by an admitted operational game source. It neither overwrites nor inherits a Schedule Fact.
_Avoid_: Merged value, assumed actual value

### Flights

**Flight**:
One planned or game-observed aircraft movement within an Operational Session that TowerGlance follows across its lifecycle. A schedule occurrence and live observations are the same Flight only when admitted evidence establishes it.
_Avoid_: Schedule row, callsign identity, cross-session flight

**Flight Identity**:
The Operational Session-scoped identity TowerGlance assigns to one Flight occurrence. It never carries across sessions and equals no single source field.
_Avoid_: Callsign identity, schedule-row ID, persistent Flight ID

**Flight Identity Evidence**:
Admitted facts that uniquely establish or continue a Flight Identity. Callsign and flight number may suffice when unique; a collision needs corroborating schedule and live facts.
_Avoid_: Globally unique callsign, mandatory hidden game ID

**Flight Identity Continuity**:
The persistence of a confirmed Flight Identity across later observations while admitted evidence proves the same occurrence. Changed facts or lost evidence never cause silent rematching.
_Avoid_: Continuous rematching, latest callsign wins, permanent source index

**Flight Correlation**:
The evidence-backed determination that a planned Flight and a game-observed Flight are the same movement. It needs one unique match with compatible direction, timing, Origin, Destination and Aircraft Type; a conflict keeps them separate.
_Avoid_: Global callsign match, nearest-time-only match, assumed merge

**Planned Phase**:
The Flight lifecycle phase before the Flight becomes controller-interactive, covering schedule-only awareness and non-interactive game presence.
_Avoid_: Scheduled phase, inactive status

**Live Phase**:
The Flight lifecycle phase from authoritative evidence of controller-interactive presence until authoritative end evidence.
_Avoid_: Game-present phase, visible-strip phase

**Ended Phase**:
The final Flight lifecycle phase, after authoritative game evidence establishes that its operational movement is over.
_Avoid_: Missing phase, removed-from-view phase

**Cancelled Flight**:
A Flight that ends from the Planned Phase on explicit authoritative cancellation evidence, without entering the Live Phase. Lateness or unobserved presence does not make a Flight cancelled.
_Avoid_: Missing Flight, late Flight

**Scheduled Flight**:
A Flight for which the Traffic Schedule Source supplies one admitted planned occurrence, for its whole lifecycle.
_Avoid_: Planned Flight, schedule row

**Unscheduled Flight**:
A game-observed Flight for which complete authoritative evidence proves that no planned occurrence exists. Missing, unavailable or ambiguous schedule evidence never makes a Flight unscheduled.
_Avoid_: Unmatched Flight, unknown schedule relationship

**Flight Direction**:
A Flight's movement relative to the Operational Session airport: Arrival or Departure.
_Avoid_: Arrival type, traffic category

**Origin**:
The airport or location from which one Flight occurrence begins. Once the Flight is game-observed, the game-derived value is authoritative.
_Avoid_: Flight Origin, previous position, current location

**Destination**:
The airport or location at which one Flight occurrence is intended to end. Once the Flight is game-observed, the game-derived value is authoritative.
_Avoid_: Flight Destination, assigned runway, current target

### Traffic Classification

**Traffic Classification**:
A source-evidenced label on one defined axis that describes a Flight. Labels from different axes are never collapsed into one exclusive traffic type.
_Avoid_: Universal traffic-type enum, scheduled category

**Airline Flight**:
A Flight whose game-owned planned occurrence supplies a valid airline and operator. Every Airline Flight is either a Passenger Flight or a Cargo Flight.
_Avoid_: Commercial Flight, non-GA Flight

**Passenger Flight**:
An Airline Flight without an explicit game-owned cargo classification.
_Avoid_: Commercial Flight, cargo-free Flight

**Cargo Flight**:
An Airline Flight that game-owned evidence explicitly classifies as a cargo service.
_Avoid_: Cargo aircraft, inferred cargo

**VFR Flight**:
A Flight that game-owned evidence explicitly classifies as operating under visual flight rules.
_Avoid_: Local Flight, Unscheduled Flight

**Local Flight**:
A VFR Flight whose Origin and Destination are the same airport.
_Avoid_: Pattern Flight, any VFR Flight

**General Aviation Flight**:
A Flight that game-owned evidence explicitly classifies as general aviation. It may also be a Scheduled, VFR or Local Flight.
_Avoid_: Unscheduled Flight, VFR Flight, Local Flight

### Aircraft and airport resources

**Aircraft**:
The game-observed physical manifestation of a Flight once Tower! Simulator 3 instantiates it. It has no identity of its own apart from its Flight.
_Avoid_: Flight, persistent airframe

**Aircraft Type**:
A game-owned aircraft type. The planned value is a Schedule Fact and the observed value is authoritative for the Aircraft; a conflict between them rejects Flight Correlation.
_Avoid_: Aircraft identity, Aircraft category, inferred type

**Regional Aircraft**:
An Aircraft whose game-owned type definition explicitly assigns the game's regional-jet category. The category says nothing by itself about weight class, size or wake category.
_Avoid_: Regional Flight, assumed small aircraft

**Wake Turbulence Category**:
The wake category of an Aircraft as explicitly supplied by game-owned data, never inferred from other characteristics.
_Avoid_: Aircraft category, inferred weight class

**Planned Aircraft Assignment**:
A Flight's Schedule Facts for its intended Aircraft Type and, when supplied, Registration. It is not an Aircraft.
_Avoid_: Planned Aircraft, scheduled airframe

**Registration**:
An optional Schedule Fact naming the registration of a Flight's planned aircraft. It is neither Flight Identity nor an Aircraft identity.
_Avoid_: Aircraft Registration, Flight ID, persistent Aircraft ID

**Terminal**:
A named static grouping of Gates in game-owned airport data. It is airport configuration, not a resource an Aircraft occupies.
_Avoid_: Gate, occupied terminal, current assignment

**Gate**:
A concrete airport resource at which an Aircraft may park.
_Avoid_: Terminal, gate eligibility, parking area in general, stand

**Terminal Eligibility**:
A static game-owned permission for an operator to use a Terminal. It never establishes a Gate Assignment or Resource Occupancy.
_Avoid_: Terminal Assignment, Gate Assignment, occupancy

**Operational Assignment**:
A game-derived direction for a Flight or Aircraft to use a concrete airport resource, such as a Gate or runway. It does not establish physical occupancy.
_Avoid_: Resource Occupancy, static permission

**Gate Assignment**:
An Operational Assignment of a Flight or Aircraft to a Gate.
_Avoid_: Gate Occupancy, Terminal Eligibility

**Resource Occupancy**:
The authoritative current physical occupation of an airport resource by an Aircraft, independent of its Flight's lifecycle phase. It may outlast an Ended Flight.
_Avoid_: Assigned resource, permitted resource

**ADIRS**:
TowerGlance's live view of an airport layout and the aircraft on it, named after the Tower! Simulator 3 airport-surface display.
_Avoid_: Airport Map

### Traffic Schedule and times

**Traffic Schedule**:
The Operational Session-scoped list of Flights admitted from the Schedule Horizon or from game observation, whatever their schedule provenance. It carries one Flight across its Planned and Live Phases.
_Avoid_: Schedule source, schedule-row list, Flight update list

**Traffic Schedule Membership**:
A Flight's inclusion in the Traffic Schedule from its admission until authoritative evidence establishes its Ended Phase or cancellation. Passing its Scheduled Time or missing live evidence does not end it.
_Avoid_: Current horizon membership, timeout removal

**Traffic Schedule Source**:
The game-owned planned traffic that applies to an Operational Session, as the game's own configuration selects it. An assumed default or partial live-traffic correlation never establishes it.
_Avoid_: Session mode, assumed default schedule, best-matching schedule

**Schedule Horizon**:
The future interval for which Tower! Simulator 3 has made traffic information available. It varies with session and game settings and is no fixed duration.
_Avoid_: Forecast window

**Scheduled Milestone**:
The lifecycle event to which a Flight's Scheduled Time refers. Times for different milestones are never interchangeable.
_Avoid_: Generic arrival, assumed landing

**Inbound Operational Entry**:
The Scheduled Milestone at which an Arrival is due to gain its first operational game presence. Controller contact and landing are later milestones.
_Avoid_: Arrival time, contact time, landing time

**Ready for Pushback**:
The Scheduled Milestone at which a Departure is due to contact the controller requesting pushback. It is not physical pushback, off-block or take-off.
_Avoid_: Departure time, off-block time, take-off time

**Scheduled Time**:
A Schedule Fact stating when a Scheduled Milestone is planned.
_Avoid_: Current time, estimated time

**Estimated Time**:
An Operational Fact or Derived Fact stating when a Scheduled Milestone is currently expected, backed by explicit evidence or a verified derivation.
_Avoid_: Assumed time, scheduled-time fallback

**Actual Time**:
An Operational Fact stating when a Scheduled Milestone occurred in Operational Session time. Without a native game event time it is the interval between the last observation before and the first observation after the occurrence.
_Avoid_: Receipt time, estimated time, exact first-observation time

**Delay**:
A Derived Fact measuring how late a Scheduled Milestone is against its Scheduled Time, keeping any uncertainty of its Actual Time. It is unknown when non-occurrence cannot be known reliably.
_Avoid_: Time since any schedule value, landing delay by default

### Stripboard

**Stripboard Mode**:
The user's explicitly selected policy, chosen on first use, by which TowerGlance advances Game-backed Strip Positions across its stripboard: Automatic Stripboard Mode or Manual Stripboard Mode. The choice persists as a preference across Operational Sessions.
_Avoid_: Automation setting, board mode

**Automatic Stripboard Mode**:
A Stripboard Mode in which TowerGlance derives when strips move between operational blocks and records their Game-backed Strip Positions without routine user action, while allowing Manual Corrections.
_Avoid_: Fully automatic board

**Manual Stripboard Mode**:
A Stripboard Mode in which the user moves each strip on either board and TowerGlance only mirrors those moves, without automatic progression or progression hints. Strip Content stays live.
_Avoid_: Manual override, Manual Mode

**Manual Stripboard Update**:
A user-triggered single pass, in Manual Stripboard Mode, that aligns every TowerGlance Strip with current In-game Strips and the authoritative lifecycle of its Flight.
_Avoid_: Automatic mode, mode switch, refresh data source

**In-game Strip**:
A game-owned strip on the Tower! Simulator 3 stripboard, with game-maintained content and a current operational block.
_Avoid_: TowerGlance Strip, planned strip

**TowerGlance Strip**:
TowerGlance's representation of one correlated In-game Strip on the same stripboard layout. It shares the In-game Strip's Game-backed Strip Position and keeps its own order within that block.
_Avoid_: In-game Strip, schedule row

**Strip Content**:
The Flight facts shown on corresponding In-game and TowerGlance Strips, such as runway and clearance indications. It determines neither the Flight lifecycle nor the Game-backed Strip Position.
_Avoid_: Strip Position, Flight state

**Game-backed Strip Position**:
The operational block a strip currently occupies in Tower! Simulator 3, shared by the in-game and TowerGlance boards. It records board placement, not lifecycle state or order within the block.
_Avoid_: TowerGlance-only position, strip order, game block

**Strip Transition Profile**:
A verified rule set that maps authoritative Operational Facts for one traffic and scenario class onto moves through the game's stripboard blocks. A profile never extends to an unverified traffic or scenario class.
_Avoid_: Universal phase sequence, header-name parser, visual layout

**Manual Correction**:
A user-directed change to a Game-backed Strip Position or to a strip's order while in Automatic Stripboard Mode. It takes precedence over unchanged facts until new authoritative Operational Facts justify a later automatic transition.
_Avoid_: Manual mode, game-board change

**Strip Reconciliation Conflict**:
The state in which an In-game Strip is absent while its Flight's authoritative lifecycle does not establish the Ended Phase. TowerGlance keeps its strip until newer evidence resolves the conflict.
_Avoid_: Completed strip, automatic removal, missing means ended

**BIN**:
The visible game-owned stripboard block to which a strip may be moved while its Flight is still active. Placing a strip there neither deletes it nor ends its Flight.
_Avoid_: Deleted strip, completed Flight

### Strip order

**Default Strip Order**:
The default order of TowerGlance Strips within one operational block: Scheduled Time Priority where handling can still be prioritized, and Block Entry Order in approach, runway-waiting and BIN blocks.
_Avoid_: Universal time sorting, proven physical queue

**Scheduled Time Priority**:
The ordering of TowerGlance Strips within a block by earliest known Scheduled Time, with Block Entry Order for ties and strips without a Scheduled Time last. It expresses handling priority, not physical aircraft sequence.
_Avoid_: Greatest delay first, physical queue position

**Block Entry Order**:
The order in which TowerGlance Strips entered one operational block; leaving and re-entering counts as a new entry. It does not establish physical aircraft sequence.
_Avoid_: Aircraft appearance order, physical queue position

**Initial Strip Order**:
The starting order of strips already in a Block Entry Order block when TowerGlance begins observing it, taken from a reliable game-provided order or else from earliest known Scheduled Time.
_Avoid_: Reconstructed entry history, continuous time sorting

**Manual Strip Order**:
A user-arranged order that replaces one block's Default Strip Order until the user restores it, with new strips appended at the bottom. It changes neither the Stripboard Mode nor automatic transitions between blocks.
_Avoid_: Manual Stripboard Mode, corrected game position
