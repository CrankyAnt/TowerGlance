# TS3 data-interface inventory

## Question and scope

Which interfaces of the current locally installed Tower! Simulator 3 (TS3) build
make game-produced data observable to a separate process, component, or service;
which have independently observed output during a game session; and do any exist
beyond `Player.log`?

This documentary, static, and bounded runtime pass concerns installed TS3 build
`v1.5.180.661s HDRP AI HOTFIX 2` on 2026-08-02. TS3 alone was launched for
Quick Play at a stock airport. A runway was selected and the session accelerated
to produce several live aircraft, strip/radar state, radio/status text, and
automatic unattended-aircraft faults; no aircraft or ATC command was given.

The elevated probe was metadata-only: it did not connect, send, request,
subscribe, capture a payload, attach a debugger, read memory, or exercise a
write capability. Its public evidence is limited to process roles, file roles,
relative event ordering, broad transport type, and sanitized counts. It contains
no endpoint values, object names, paths, raw traffic, keys, identifiers,
credentials, personal data, or proprietary bulk content.

After the derived evidence was incorporated, all issue-specific research-owned
raw metadata captures and temporary probe scripts were deleted from the local
workspace. No packet/payload capture or raw log content was retained, and the
TS3-owned ephemeral configuration was not copied into the repository.

The ledger is mechanism-based, not a list of examples. “Investigated but not
observed” is a result for this build and scenario only; it is not proof of
absence in another session or build.

Local provenance is separated by method: persistent-file metadata observation
([L2]); process-tree and socket-metadata observation ([L3]); user-authorized GUI
phase observation ([L4]); and post-session static inventory of TS3-owned
components, ephemeral configuration roles, and service registration ([L5]).

## Direct answer

**Yes: the current build independently shows TS3-owned interface mechanisms
beyond `Player.log`, including first-party loopback IPC to local AI components
and to built-in external-display children. No: beyond the diagnostic-log family,
this pass observed no independently verified game-produced output during a game
session. It observed lifecycle/transport candidates, not their payload or
rendered output, and therefore establishes no readable non-log live-state feed,
public API, or safe TowerGlance-consumable interface.**

- `Player-prev.log` is an observed prior-session member of the same diagnostic
  log family, not an independent live mechanism. FeelThere Support identifies it
  as the session before the last one. [S1]
- The main TS3 process formed established loopback pairs with first-party local
  text-to-speech, recognition, and parser children during the live session.
  Metadata establishes process ownership/lifecycle and transport presence, not
  message direction, framing, or game-state content. [L3]
- Invoking TS3's built-in Add Window feature started TS3-owned external-display
  children, with corresponding new loopback pairs. Their lifecycle ended when
  returning to the menu. This is the strongest candidate for a TS3-owned live
  display/state transport, but that interpretation remains an inference because
  the payload, rendered output, and client semantics were not inspected.
  [L3][L4]

`Player.log` remains supplementary diagnostic evidence rather than an
authoritative Operational Session contract. [S3][S4]

## Clean-room external-lead boundary

**Maintainer decision.** External companion applications are not TS3-owned
components or candidates in this ledger. A separate static, no-launch clean-room
inspection may generate only general hypotheses about TS3-owned interfaces; its
results are not published here as evidence.

No external application may become a TowerGlance dependency, and no external
code, protocol, private implementation detail, data stream, authentication,
credential, asset, or installation content may be copied, reused, or published.
Every hypothesis must be independently verified against TS3 before it can become
a conclusion in this inventory.

Independent public technical leads were also screened. They produced no
reliable claim of a TS3 protocol, API, or live-state feed. The retained
independent index only mirrors first-party update notes; its generic technology
listings were not promoted to TS3-use claims without local verification. [S8]

## Evidence-led coverage ledger

| Mechanism class | Status | Evidence and minimal signature | Unknowns / safety boundary |
| --- | --- | --- | --- |
| Current diagnostic log | **observed interface with output** | File-based append/reset text diagnostic stream. Output was observed in prior controlled sessions. Persistent-file metadata observation established it as the only changed file family in the monitored installation and persistent user-data roots. [S3][S4][L2] | Undocumented mixed format; no stable session, aircraft, flight, strip, or lifecycle identity is established. Ephemeral child configuration is a separate row outside those monitored roots. |
| Previous diagnostic log | **observed interface with output** | Text prior-session log in the same diagnostic family; official support identifies the broad rotation role, and persistent-file metadata observation during the current-build launch independently reproduced a metadata-level rotation/change. [S1][L1][L2] | It is not a separate live feed, and no content was retained or reproduced. |
| Generated child configuration | **candidate requiring a separate probe** | Post-session static inventory found ephemeral first-party JSON configuration associated with local AI children. Sanitized role-level inspection found local resource/configuration plus host/port fields. [L5] | Creation/change during a game session was not observed, so game-produced output is not established. The files were outside the persistent roots monitored by [L2]; exact fields/values remain private. |
| Installed airport/database/instrument resources | **irrelevant because no game-produced data** | JSON/CSV/configuration resources expose static airport geometry, schedules, terminals, recognizer, and presentation configuration. [S2] | Useful static inputs, but not session-produced output; live selection/linkage remains unproven. |
| Other generated user state, saves, temporary files, cache | **investigated but not observed** | Apart from the separately listed ephemeral child configuration, persistent-file metadata observation established no changed game-owned file family beyond diagnostic logs in its monitored roots. [L2][L5] | This does not exclude files created in other modes, timings, airports, roots, or future builds. |
| Crash dumps and bug-report package | **candidate requiring a separate probe** | Official notes say the in-game bug-report action gathers a package. [S5] | Invoking it creates output and is outside this observational pass. Contents and relation to live state are unknown. |
| First-party local AI child processes | **candidate requiring a separate probe** | Official material describes local text-to-speech and recognition. Game-root-filtered process-tree observation established first-party text-to-speech, recognition, and parser children during the session; they exited on return to menu. [S5][S6][L3] | Process presence establishes a component boundary, not output, exact responsibility, data contract, or Operational Session identity. |
| TCP listeners/connections | **candidate requiring a separate probe** | Owner-attributed socket-metadata observation found 13 TS3-owned TCP records at baseline, including one loopback listener and established main/AI-child/self pairs. Add Window raised the count 13 → 16 → 19; menu return reduced it 19 → 3 → 0 as children exited. [L3][L4] | Socket metadata establishes interface presence, not output, payload, framing, message direction, authentication, read-only client behavior, or a public API. Do not connect. |
| Built-in external display | **candidate requiring a separate probe** | GUI phase observation started the TS3 Add Window feature. Correlated process/socket observation found two TS3-owned external-display players plus crash handlers and a new main-process loopback pair per child; they exited on menu return. [L3][L4] | Live display/state transport is a strong inference, not observed output. Rendered content and payload were not inspected; compatibility and safe consumption remain unknown. |
| UDP endpoints/bindings | **candidate requiring a separate probe** | Owner-attributed socket-metadata observation found 26 wildcard UDP bindings for the main process. [L3] | No UDP traffic, direction, peer role, framing, or game-produced content was observed. A passive metadata-only phase-correlation probe may be justified; no packet capture. |
| Other local/remote network services | **candidate requiring a separate probe** | TS3 publicly advertises Online Co-op; platform networking documentation describes available mechanisms but does not prove TS3's use. [S6][S7] | Restrict any follow-up to TS3-owned metadata; do not authenticate, query services, or interact with a remote endpoint. |
| Named pipes | **investigated but not observed** | A global namespace-difference observation saw system-wide churn but could not attribute any pipe to a TS3-owned process. [L3] | Inconclusive: do not treat system-wide activity as TS3 evidence. A future probe needs owner attribution without opening a pipe. |
| Shared memory, memory-mapped files, anonymous IPC | **candidate requiring a separate probe** | No owner-attributed object/handle inspection was performed, so this mechanism has no positive or negative runtime result. | A live handle/object probe may be considered only if it can avoid object reads and publication of names. |
| Process modules | **investigated but not observed** | Runtime module-basename inventory for game-root-owned processes did not establish a distinct data-publication mechanism. [L3] | Module presence is capability context, not output or a data contract; binary implementation details are excluded from the public evidence. |
| Process handles | **candidate requiring a separate probe** | Raw handle/object enumeration was deliberately omitted because it could cross the object-read and publication boundaries. | A separate owner-attribution method is needed before this mechanism can receive a positive or negative result. |
| Windows services | **investigated but not observed** | A post-session, installation-root-filtered Windows service inventory found no TS3-install-owned service registration; the runtime process tree showed no additional TS3-owned service role. [L3][L5] | This build/scenario-bound result does not exclude publisher components installed elsewhere or future service-backed behavior. |
| Windows registry and OS-visible configuration/activity | **investigated but not observed** | Post-session static inventory established no TS3 registry-backed data-publication route. [L5] | A future read-only trace must filter to TS3-owned effects and avoid unrelated user data. |
| Unity/player diagnostic facilities | **investigated but not observed** | Unity documentation plausibly explains the already-counted player-log family; local component inventory identified the main and external-display players as Unity-based. [S9][L5] | This does not establish a separate TS3 output interface, schema, endpoint, or complete output inventory. Do not change launch/logging settings. |
| Engine/plugin/extension/mod interfaces | **investigated but not observed** | Store material advertises customization, while the local static inventory identified content resources rather than a documented runtime extension API. [S6][S2] | Do not infer an API from customization or inspect external implementations. |
| In-game radio message log | **candidate requiring a separate probe** | GUI phase observation visibly produced radio/status text, and official notes describe an in-game radio log. [L4][S5] | No externally observable artifact or transport was established. A GUI-authorized output study is separate work. |
| Platform account, cloud, achievements and overlays | **irrelevant because no game-produced data** | No TS3-published operational-data route was established; platform capability documentation is not TS3-use evidence. [S7] | Do not query account, cloud, authentication, or third-party service data. |

## Facts, inference, and maintainer decision

**Observed facts.** During the specified Quick Play session, TS3 created and
ended first-party local AI and external-display children in correlation with
game phases. TCP metadata changed with those lifecycles. The diagnostic-log
family provided the only independently verified game-produced output: changed
text-file metadata in the monitored persistent roots. The runtime pass also
observed wildcard UDP bindings; it did not observe UDP traffic. [L2][L3][L4]

**Inference.** The Add Window correlation is strong evidence that loopback IPC
likely transports live display/state between TS3-owned processes. The AI-child
pairs may carry speech/recognition/parser-related data. Neither inference
establishes that any payload was observed, that it contains operational state,
or that it has safe external-consumer semantics.

**Maintainer decision.** Treat all non-log interfaces as unvalidated candidates,
not TowerGlance inputs. A handshake, request, subscription, client connection,
payload capture, or write-capability exercise requires a separately bounded
prototype. No downstream issue is created by this research pass.

## Completeness argument and residual uncertainty

The model covers persistence/generated/cache/save, diagnostics, TCP/UDP,
pipe/shared-state/IPC, process/child/module/handle/service boundaries,
OS-observable activity, engine/platform/extension/debug, and local/remote
services. “Covers” includes explicit candidate/gap rows: handle and shared-object
inspection was deliberately not performed. The model is backed by a Quick Play
runtime lifecycle, including accelerated unattended activity and the built-in
external-display feature, rather than static evidence alone.

It is still not exhaustive. It does not establish payloads, framing, direction,
identity, ordering, freshness, permissions, authentication, version stability,
other airport/mode behavior, multiplayer behavior, or any client compatibility.
Named pipes remain inconclusive, shared-memory objects were not established,
and UDP had bindings only. An absence finding is scenario- and build-bound.

## Ranked next steps

1. **Passive phase-correlation repeat.** Repeat the metadata-only probe across
   launch, menu, Quick Play, scheduled mode, external-display start/stop, and
   restart to test lifecycle stability and ephemeral configuration
   appearance/change without opening interfaces.
2. **Passive external-display ownership study.** Correlate the built-in display
   child lifecycle with visible state changes and sanitized TCP metadata only;
   continue to avoid payload capture and connection.
3. **Passive IPC attribution.** Improve named-pipe/shared-object attribution so
   global OS churn cannot be mistaken for TS3, without opening or reading an
   object.
4. **Separate bounded interaction prototype, only if justified.** Define a
   threat model and stop conditions before any handshake, request, subscription,
   or client connection to a TS3-owned listener.
5. **Radio-log/bug-report output study.** Authorize separately because it uses
   GUI actions and may create files; retain only sanitized derived evidence.

No observed interface is recommended as suitable for TowerGlance until output,
ownership, semantics, stability, and read-only viability are independently
evidenced.

## Sources

### Primary/public sources

- **S1 — official support documentation:** [How to obtain your game log
  files](https://feelthere.zendesk.com/hc/en-us/articles/18584181148188-How-to-obtain-your-game-log-files)
  (FeelThere Support identifies `Player.log` as the last session and
  `Player-prev.log` as the preceding session).
- **S5 — current official update announcement:** [Major Tower Simulator 3
  Update 7](https://steamcommunity.com/games/2176130/announcements/detail/681878780522793839)
  (radio message log and manual bug-report package).
- **S6 — official store listing:** [Tower! Simulator 3 on
  Steam](https://store.steampowered.com/app/2176130/Tower_Simulator_3/) (local
  neural voice/recognition features, Online Co-op, and customization; not an API
  specification).
- **S7 — platform documentation:** [Steam Networking
  documentation](https://partner.steamgames.com/doc/features/multiplayer/networking)
  (platform mechanics, not TS3-use evidence).
- **S8 — independent public index/mirror:** [SteamDB Update 7
  record](https://steamdb.info/patchnotes/23732564/) (mirrors the first-party
  update notes; generic technology listings are hypotheses only and are not
  evidence of TS3 interface use).
- **S9 — engine documentation:** [Unity log files](https://docs.unity3d.com/Manual/log-files.html)
  (general engine log behavior; not a TS3 schema specification).

### Independent local evidence

- **L1 — TS3 user-data static inventory (2026-08-02):** current and previous
  Player logs were present and text-readable; the cache directory had no files.
  No content was retained or reproduced.
- **L2 — persistent-file metadata observation (2026-08-02):** timestamp/length
  observation in the validated game installation and persistent user-data roots.
  It reproduced current/previous log-family changes without retaining content,
  paths, or hashes.
- **L3 — process/socket metadata observation (2026-08-02):** game-root-filtered
  process roles, parent/lifecycle, module basenames, endpoint classes, socket
  states, salted connection tokens, and a global pipe-namespace difference. No
  connect, payload, endpoint value, object name, or raw traffic was retained.
- **L4 — user-authorized GUI phase observation (2026-08-02):** launch, main
  menu, stock-airport Quick Play, accelerated unattended activity, Add Window,
  menu return, and exit. No aircraft or ATC command was issued and no persistent
  setting was changed.
- **L5 — post-session static local inventory (2026-08-02):** sanitized
  TS3-owned component/configuration roles plus an installation-root-filtered
  service and registry check. No code, binary implementation detail, endpoint,
  path, field value, or object name is published.
- **S2 — repository local evidence:** [installed airport and schedule coverage](ts3-installed-airport-schedule-data-coverage.md).
- **S3 — canonical earlier controlled evidence:** [issue #4 result comment](https://github.com/CrankyAnt/TowerGlance/issues/4#issuecomment-5156717657).
- **S4 — repository local evidence:** [Player.log lifecycle coverage](ts3-player-log-event-lifecycle-coverage.md).
