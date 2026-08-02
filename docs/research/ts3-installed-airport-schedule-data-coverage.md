# TS3 installed airport and schedule data coverage

## Question and scope

This read-only, clean-room investigation asks which locally installed, game-owned
Tower! Simulator 3 files can provide the airport, schedule, ADIRS and stripboard
information relevant to TowerGlance v1. It inspected file names, formats, headers,
counts, and JSON structure from a representative installed official content set on
2026-08-02. It did not start the game, inspect another companion product, copy
bulk content, or treat an undocumented file as a runtime contract.

The observations below describe this installation only. They do not establish that
every separately installed airport package, game version, or running session uses
the same files.

## Findings

### Coverage at a glance

| v1 information | Observed candidate source | Observed coverage | Confidence and limitation |
| --- | --- | --- | --- |
| Airport identity and geographic reference | `airports.csv` in airport database profiles | Seven columns: ICAO, name, latitude, longitude, IATA, GMT offset, country code. | Strong static-index evidence; it is not a runway/taxiway geometry source. [E1] |
| Airport map geometry and configuration | `<ICAO>.airport` JSON | 29 installed files, each with the same top-level schema and 64--517 roads / 463--2,901 knots. | Strong evidence for static geometry data; road-type numeric semantics and selection at runtime are unverified. [E2] |
| Gates and terminal assignment | `.airport` JSON knots plus `terminals.csv` | Every inspected knot has position, gate-related fields and a road-reference list; `terminals.csv` relates terminal names, allowed operators and gate-road names. | Useful static candidates, but the linkage and gate-type codes need runtime validation. [E2][E3] |
| Runways/taxiways | `.airport` JSON roads and knots; recognizer lexicon | Roads expose geometry/configuration fields; the recognizer lexicon has runway and taxiway pronunciation collections. | Geometry is present in advanced packages; no independently verified type-code legend or live occupancy/state. [E2][E5] |
| Scheduled airline flights | `schedule.csv`; optional `schedule_career.csv`; `ga.csv`; `airlines.csv` | `schedule.csv` has ten flight columns; career schedules add registration; GA has a separate 13-column format; airline metadata supplies airline/callsign/name/country. | Strong static schedule evidence, not evidence of active flights, strip order or dispatch state. [E3] |
| Frequencies/control-area labels | `freq.csv` | Five labelled columns including frequency and control area. | Static configuration only. [E3] |
| Stripboard appearance/structure | `striplook.csv` in instrument profiles | 42 installed profiles; key/value and layout records, with varying line count and no shared CSV-header contract. | Appearance/layout configuration, not flight-strip instances or live state. [E4] |
| ADIRS appearance/configuration | `adirslook.csv` in instrument profiles | 42 installed profiles; key/value style/configuration records, not a tabular header contract. | Appearance/configuration only; not a live ADIRS feed. [E4] |
| Speech-recognition vocabulary | `RECOG/config/session_lexicon.json` | Collections for airplanes, taxiways, runways, gates, points and frequencies; items carry identifiers and pronunciations. | Supports names/recognition, not simulation state. [E5] |

### Observed package variants

**Fact.** The installed `Airports` tree contains 75 ICAO-like airport directories.
There are 114 direct database-profile directories: 113 contain `package.txt` and
one is incomplete. Sixty-six have the common seven-file bundle (`airlines`,
`airports`, `freq`, `ga`, `schedule`, `terminals`, `package`). Across all profiles,
16 contain `schedule_career.csv` and nine contain `local_traffic_cfg.csv`; the
exact common-bundle variants containing those files number ten and seven
respectively. A small number have a package-local `.airport` JSON or another
missing/extra member. [E1]

**Fact.** There are 29 `.airport` JSON files and 42 instrument-profile directories.
The standard instrument profile contains `adirslook.csv`, `dbrightlook.csv`,
`striplook.csv`, `weatherlook.csv`, and `package.txt`; one observed profile adds
colour files. The airport layout/configuration profiles therefore outnumber the
JSON geometry files and are not one-to-one. [E1][E4]

**Inference.** TowerGlance should discover available profiles per selected airport
rather than assuming one default schedule, geometry, or display configuration.
The selection rule is not established by static inspection.

### Formats and minimal schema observations

**Fact.** Database files are comma-delimited CSV. The regular schedule header is:
operator colour, airline callsign, flight number, aircraft type, origin ICAO,
destination ICAO, approach time, departure time, approach-altitude override and
special field. `schedule_career.csv` has the same columns plus registration.
`terminals.csv` has terminal name, allowed operators and terminal gate-road-name
list. [E3]

**Fact.** Each inspected `.airport` file is JSON with airport reference/configuration
fields (centre latitude/longitude, altitude, time/wind fields, ICAO, display/name,
tower and ground callsigns, version) and a `roads` array. A representative file
had 360 roads and 1,974 knots. Each road had name/readback/display fields,
numeric operational/configuration fields, and a `knots` array. Each knot had an
`x/y/z` position, gate type, saved gate-road references, name and spoken name.
[E2]

**Inference.** The JSON is the strongest installed candidate for rendering an
airport graph and associating gates with it. The semantics of unknown numeric
codes and missing values remain unresolved; mapping them to domain values is a
separate maintainer decision after runtime validation.

## Required information absent or uncertain

- No inspected trusted installed file demonstrated a live session contract for
  selected airport/profile, current aircraft, strip creation/order, runway
  occupancy, controller actions, or schedule-to-live-flight matching.
- The `.airport` JSON road `type`, gate `gatetype`, road-reference direction, and
  numeric operational fields are undocumented. Their meanings cannot safely be
  inferred from the field names alone.
- Only 29 of the 75 airport directories expose an inspected `.airport` JSON, so
  a geometry-dependent v1 cannot assume coverage for the remaining directories.
- `adirslook.csv` and `striplook.csv` are presentation configurations; they do
  not provide the current content of an ADIRS or stripboard.
- Binary `.asset` files and preview images were inventory-only; they are not a
  supported data interface established by this research.
- Logs under the local application-data area were intentionally not used as an
  evidence source for public schema claims. No live game was run.

## Sources and evidence

All evidence is from the locally installed Tower! Simulator 3 content, examined
read-only on 2026-08-02. No absolute installation paths, raw schedules, log lines,
or personal data are reproduced.

- **E1 -- airport/package inventory:** direct-child enumeration of every
  `Airports/<ICAO>/databases` directory, plus file inventory and CSV bundle counts.
  It found 75 airport directories, 114 database profiles, 29 JSON layouts and the
  named CSV bundles.
- **E2 -- JSON structure scan:** JSON parse of all 29 `.airport` files, grouped by
  top-level keys and road/knot counts; a representative schema/property scan.
- **E3 -- CSV schema scan:** header-only reads of representative `airports.csv`,
  `terminals.csv`, `schedule.csv`, `schedule_career.csv`, `ga.csv`, `airlines.csv`
  and `freq.csv`; line/column counts were used without copying records.
- **E4 -- instrument-profile scan:** inventory of 42 profile directories plus
  header/line-count inspection of `adirslook.csv` and `striplook.csv`.
- **E5 -- recognizer configuration scan:** JSON collection/property inspection of
  the installed `session_lexicon.json`; no speech content or user data was copied.

## Next steps

1. Capture a deliberately short, sanitized live TS3 session under a separate
   evidence plan to establish the selected package/profile and live state source.
2. Validate JSON road and gate numeric-code semantics against controlled in-game
   observations before treating them as domain enums.
3. Define a v1 coverage policy for airports without `.airport` JSON: unsupported,
   static-index-only, or an independently observed fallback.
4. Treat schedule/terminal imports as static and versioned; do not claim live
   stripboard or aircraft state until the runtime link is observed.
