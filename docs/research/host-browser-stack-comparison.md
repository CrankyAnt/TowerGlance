# Host and browser stack comparison

## Question and scope

Issue #13 asks which currently viable technology stacks satisfy the
established game-source access, browser delivery, local storage, Windows
packaging, WCAG, testing, and maintainer-ergonomics constraints for the
TowerGlance v1 TowerGlance Host and browser experience, and what
evidence-backed trade-offs distinguish them. It is a comparison and a ranked
engineering inference for the later architecture decision in issue #14. It
is not that decision and not a specification.

Evidence was collected on 2026-09-09 from primary sources: runtime release
and support pages, W3C and WHATWG specifications, MDN, Microsoft Learn,
python.org, nodejs.org, go.dev, and first-party project repositories and
documentation. Every date-sensitive claim was checked against the owning
source on that date; access dates are recorded per source. Documented facts
and engineering inference are separated throughout; a statement marked
"Inference" is not backed by a primary source and must be treated as such by
the downstream decision.

The constraints are the seven named in issue #13 plus realtime transport and
frontend framework as explicit rows. Their origin is repository evidence and
maintainer decisions already published on the map [R1] to [R7]; this artifact
does not restate unpublished Communication Port detail and treats the
interface shape as established.

## Direct answer

No stack dominates every constraint. Four stacks are currently viable for a
v1 TowerGlance Host that serves normal, already-installed browsers on the
same Windows machine, ranked by engineering inference:

1. **.NET (C#) with ASP.NET Core / Kestrel.** Built-in, unelevated process
   and TCP-listener enumeration for Same Session proof [S5][S6], a
   first-party SQLite provider [S7], WebSockets fully supported under Native
   AOT and SignalR partially supported with named limitations [S3][S4],
   self-contained single-file publishing without a preinstalled runtime
   [S2], documented Windows Service and notification-area (tray) hosting
   [S8][S9], an official Playwright binding [S33], and a Long Term Support
   runtime supported to 2028-11-14 [S1]. It matches the maintainer's growing
   C# investment.
2. **Python with asyncio and FastAPI/Starlette.** Best fit for the
   maintainer's primary language: newline-delimited TCP reads are a stdlib
   primitive [S12], WebSockets are a documented FastAPI feature [S13],
   `sqlite3` is stdlib, `psutil` documents no Windows privilege requirement
   for connection enumeration [S14], Playwright has an official Python
   binding [S33], and python.org ships an embeddable Windows distribution
   intended for inclusion in another application [S11]. Its weaker link is
   packaging trust: PyInstaller maintainers confirm recurring antivirus
   false positives for one-file builds and recommend one-folder builds and
   vendor reports as mitigation [S15]; no primary source quantifies the
   residual risk.
3. **Go with `net/http`.** Single static binary, a CGo-free SQLite driver
   with documented Windows amd64 support [S23], and an actively maintained
   BSD-licensed WebSocket library [S22]. Its costs are a new language for
   the maintainer, a rolling support window of roughly one year per major
   release [S21], and no official Playwright binding [S33].
4. **Node.js / TypeScript, or Bun or Deno.** Shares a language with the
   frontend, and Bun and Deno both compile standalone Windows executables
   [S19][S20]. Against it: Node core has no process or listener enumeration
   API, `node:sqlite` is a Release Candidate in both Node 24 and 26 [S17],
   and Node's single-executable feature is still marked "Active
   development" in Node 24 [S18].

Rust is technically comparable to Go but adds the steepest ramp-up for this
maintainer and also lacks an official Playwright binding [S24][S33]
(ramp-up is inference). Lua-based servers are not viable as the Windows host
runtime: OpenResty's own Windows README states IOCP is unsupported and
warns against production use with high concurrency [S25], and Luvit's
Windows install is a PowerShell bootstrap script with prebuilt Windows
binaries still marked as unpublished [S26]. Electron or Tauri cannot be the
UI surface because v1 requires normal browser windows [R1]; as a host
wrapper only, neither adds anything a native tray process lacks (inference).

Two cross-stack facts matter more than the language choice: WebSocket is
the transport that avoids the documented six-connection limit of
`EventSource` over HTTP/1.1 when several windows are open [S27], and every
stack faces the same unsigned-download friction from SmartScreen and Smart
App Control until a signing route exists [S35].

## Constraints compared

| Constraint | Origin | What a stack must offer |
| --- | --- | --- |
| Game-source access | [R2][R3][R4][R5][R7] | Windows loopback TCP client with newline-delimited reads, silence and reconnect tolerance, per-source parse isolation, one bounded acknowledged write with exact readback, process-instance and listener observation for Same Session proof |
| Browser delivery | [R1][R6] | Serve current Edge, Chrome, and Firefox on the same device; coherent baseline plus ordered updates per window; host-serialized commands; loopback-only Local Access; host lifetime independent of any window |
| Local storage | [R6] | Persistent Stripboard Mode preference; bounded Recovery Snapshot inside the 30-minute Recovery Window; sanitized local logs; read-only access to installed game files |
| Windows packaging | [R1] | Install without a developer toolchain or terminal; no mandatory runtime download; background or tray process; launch, update, port-conflict, and uninstall story |
| WCAG 2.2 AA | [R1] | English UI meeting AA including the 2.2 criteria that constrain a stripboard |
| Testing | [R1] and `docs/agents/delivery.md` | Unit tests, protocol tests against a TowerGlance-owned fake Communication Port, browser end-to-end and accessibility checks, GitHub Actions Windows runners |
| Maintainer ergonomics | [R1] | Solo maintainer; primary Python and Lua, growing C#; typing and tooling support; dependency surface; runtime support window |

## Constraint by stack matrix

Cells summarise documented facts with source ids; "Inference" marks
judgement without a primary source.

| Constraint | .NET / C# | Python | Node.js / Bun / Deno | Go | Rust | Lua (Luvit / OpenResty) | Electron / Tauri |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Game-source access | `Process.StartTime` and `GetActiveTcpListeners` in the base library; no privilege requirement documented [S5][S6] | asyncio `readline` and `readuntil` [S12]; `psutil.net_connections()` with no Windows privilege note [S14] | `net` sockets in core; no core process or listener enumeration (Inference: needs a native addon or shell-out) | `net` in stdlib; process and listener enumeration via `golang.org/x/sys/windows` or third-party packages (Inference) | tokio sockets; enumeration via crates (Inference) | No documented facility found | Inherits the wrapped host language |
| Browser delivery | Kestrel WebSockets fully supported, SignalR partially, under Native AOT [S3] | FastAPI or Starlette WebSockets with the `websockets` package [S13] | `ws` or Fastify (Inference: maturity) | `gorilla/websocket`, actively maintained, BSD-2 [S22] | axum plus tokio-tungstenite (Inference: maturity) | OpenResty Windows build excluded from high-concurrency production by its README [S25] | Conflicts with the normal-browser-window boundary as a UI surface [R1] |
| Local storage | `Microsoft.Data.Sqlite`, first-party ADO.NET provider [S7] | stdlib `sqlite3` | `node:sqlite` Release Candidate in Node 24 and 26 [S17] | `modernc.org/sqlite`, CGo-free, Windows amd64 supported [S23] | `rusqlite` (Inference: maturity) | LuaJIT FFI bindings only (Inference) | Inherits host language |
| Windows packaging | Self-contained, single-file, trimmed, or Native AOT publish without a preinstalled runtime [S2]; Windows Service needs administrator installation [S8]; tray via `NotifyIcon` [S9] | PyInstaller one-folder recommended by maintainers over one-file [S15]; embeddable distribution for bundling inside an installer [S11] | Node SEA "Active development" in Node 24 [S18]; Bun and Deno compile Windows executables and cross-compile [S19][S20] | Single static binary (Inference: simplest packaging) | Single static binary (Inference) | No documented single-file Windows story | Ships a browser engine or webview only to host a background process (Inference) |
| WCAG 2.2 AA | Framework-independent; official Playwright .NET binding for automated checks [S33] | Official Playwright Python binding [S33] | Official Playwright JavaScript binding [S33] | No official binding; tests run from another language [S33] | Same gap as Go [S33] | Same gap as Go | Frontend concern, independent of shell |
| Testing | xUnit or NUnit plus Playwright .NET [S33] | pytest plus Playwright Python [S33] | node test runner or Vitest plus Playwright [S33] | `go test`; browser tests via a second toolchain [S33] | `cargo test`; browser tests via a second toolchain [S33] | No first-party Windows CI guidance found | Inherits host language |
| Maintainer ergonomics | Growing C# experience; static typing; LTS to 2028-11 [S1] | Primary language; optional typing; 3.13 and 3.14 supported to 2029-10 and 2030-10 [S10] | New language; LTS lines to 2028-04 (24) and 2029-04 (26) [S16] | New language; one-year rolling support [S21] | New language; six-week stable train [S24] | Primary language, but no viable host runtime | Extra runtime and build surface on top of the host language |
| Realtime transport | WebSocket; `EventSource` limited to six connections per browser and domain over HTTP/1.1 [S27] | same | same | same | same | same | n/a |
| Frontend framework | Any; build tooling is development-time only (Inference) | Any | Any | Any | Any | Any | n/a |

## Cross-stack factors

### Realtime transport

- **WebSocket.** Full-duplex, message-ordered per connection [S28]. One
  connection per window carries both the ordered state stream and the
  commands, which fits the per-window baseline-plus-updates and
  host-serialized command requirements in [R6] directly (inference on fit).
- **Server-Sent Events plus POST.** `EventSource` auto-reconnects, but MDN
  documents that when not used over HTTP/2 it is limited to six open
  connections per browser and domain, a limit marked "Won't fix" in both
  Chrome and Firefox [S27]. TowerGlance's multi-window requirement [R6]
  makes that cap a concrete risk. HTTP/2 lifts the cap, but major browsers
  negotiate HTTP/2 only over TLS in practice, which would reintroduce
  local certificate management (inference; not verified against current
  browser behaviour for cleartext HTTP/2).
- **Long polling.** Same connection-pool exposure as SSE with higher
  latency and no native ordering (inference).

### Browser per-window state

The HTML Standard scopes `sessionStorage` to one top-level browsing
context: each window or tab opened on the site has its own copy, while
`localStorage` is shared across all windows of the origin [S30]. The
independent per-window navigation, zoom, and filter state required by [R6]
therefore belongs in `sessionStorage` or the URL, and shared state must not
be cached in `localStorage` as if it were authoritative (inference on
application; the scoping is documented).

### Loopback origin, secure context, and Local Network Access

- The W3C Secure Contexts specification treats origins whose host is
  `localhost`, matches `127.0.0.0/8`, or is `::1` as potentially
  trustworthy [S29]. A loopback host can therefore use secure-context Web
  APIs over plain HTTP; TLS is not a v1 packaging requirement.
- Chrome's Local Network Access permission applies to requests *from* the
  public network *to* a local network or loopback destination, shipping in
  Chrome 142 [S31]. A page served from the loopback origin that talks back
  to the same loopback host is not that case. Chrome states an intent to
  extend protection to cross-origin local requests later, so the boundary
  should be re-checked before release (inference on the follow-up).

### Windows Defender Firewall (unresolved)

Whether a listener bound only to `127.0.0.1` never triggers the Windows
Firewall "allow access" prompt was not confirmed against a first-party
Microsoft document in this pass. Only Microsoft Q&A community threads were
found. It is recorded as an uncertainty and a prototype check, not a fact.

### Code signing, SmartScreen, and Smart App Control

- Microsoft Defender SmartScreen evaluates publisher reputation and file
  hash reputation. An unsigned or self-signed download shows the "Windows
  protected your PC" warning until reputation accumulates, and unsigned
  files start from zero reputation with every new version. Signed files
  from a consistent publisher identity carry reputation across versions.
  EV certificates no longer bypass SmartScreen. On Windows 11, Smart App
  Control may block unsigned files without positive reputation regardless
  of download origin [S35].
- Microsoft's managed signing service is now called Azure Artifact Signing
  (formerly Trusted Signing). Public Trust certificates are available to
  organizations in the United States, Canada, the European Union, the
  United Kingdom, Australia, New Zealand, Japan, South Korea, Singapore,
  Switzerland, Norway, and Israel, and to individual developers located in
  the United States or Canada [S36]. Organizations must show three or more
  years of verifiable tax history; the Basic tier is priced at roughly ten
  US dollars per month [S34][S35]. Whether the maintainer qualifies is not
  established here and is an input to issue #14.
- MSIX packages must be signed with a certificate that chains to a trusted
  root on the device [S34]. A plain installer or self-contained executable
  can ship unsigned at the cost of the SmartScreen friction above.
- These facts apply identically to every stack; signing is a publishing
  decision, not a language differentiator.

### Installer tooling licensing

- **Inno Setup** grants permission to anyone to use it for any purpose,
  including commercial applications, under a short attribution-style
  license [S37].
- **WiX Toolset** is licensed under the Microsoft Reciprocal License, and
  its repository requires an Open Source Maintenance Fee only from users
  whose revenue-generating use reaches an annual gross revenue of at least
  ten thousand US dollars; hobby and non-revenue use is exempt [S38].
- **Velopack** is MIT-licensed and documents Windows, macOS, and Linux
  installers with auto-update for C#, C++, JavaScript, and Rust [S39].

### WCAG 2.2 AA criteria for a stripboard

WCAG 2.2 is a W3C Recommendation, first published 2023-10-05 with the
current edition dated 2024-12-12 [S32]. Three 2.2 criteria constrain a
drag-oriented stripboard directly:

- **2.5.7 Dragging Movements (AA):** functionality that uses dragging must
  be operable by a single pointer without dragging unless dragging is
  essential. Strip moves need a non-drag alternative such as select then
  choose a target block [S32].
- **2.5.8 Target Size (Minimum) (AA):** pointer targets must be at least
  24 by 24 CSS pixels unless a smaller target or spacing is essential [S32].
- **2.4.11 Focus Not Obscured (Minimum) (AA):** a focused component must
  not be entirely hidden by author-created content such as sticky headers
  or panels [S32].

No frontend framework enforces these automatically; compliance depends on
component implementation regardless of stack (inference). Automated checks
can run with axe-core through Playwright's official bindings for
JavaScript, Python, Java, and .NET [S33].

### Testing tooling

Playwright officially supports JavaScript and TypeScript, Python, Java, and
.NET, and no other language [S33]. A Go or Rust host therefore needs a
second toolchain for browser and accessibility tests. GitHub Actions
Windows runners are general platform knowledge and were not separately
cited. A TowerGlance-owned fake Communication Port for protocol tests is
possible in every candidate language because the wire is newline-delimited
text over TCP [R2][R7] (inference on effort parity).

## Per-stack sections

### .NET (C#)

**Documented facts.** .NET 10 is the current LTS release (released
2025-11-11, supported to 2028-11-14); .NET 8 (LTS) and .NET 9 (STS) both
end support on 2026-11-10; LTS releases receive three years and STS
releases two [S1]. Self-contained publishing bundles the runtime so the
target machine needs no preinstalled .NET; single-file, trimmed, ReadyToRun,
and Native AOT variants are documented [S2]. Under Native AOT, WebSockets
are fully supported, Minimal APIs and SignalR partially, and MVC and Blazor
Server not at all [S3]. SignalR's documented Native AOT limitations are
that strongly typed hubs are unsupported under `PublishAot` and that hub
parameters of `IAsyncEnumerable<T>` or `ChannelReader<T>` with value-type
`T` throw at startup [S4]. `IPGlobalProperties.GetActiveTcpListeners`
returns the local TCP listeners through the Win32 `GetTcpTable` call and
documents no privilege requirement [S5]; `Process.StartTime` is available
for local processes [S6]. `Microsoft.Data.Sqlite` is the first-party
ADO.NET provider maintained with Entity Framework Core [S7]. A worker can be
hosted as a Windows Service through `Microsoft.Extensions.Hosting.WindowsServices`,
but creating the service with `sc.exe` requires an Administrator session
[S8]; a notification-area icon is available through Windows Forms
`NotifyIcon` [S9].

**Inference.** A per-user tray process avoids the administrator step that a
Windows Service needs, which matters for a toolchain-free install. Raw
WebSockets sidestep every SignalR Native AOT limitation; SignalR remains
available under a trimmed non-AOT publish. Published size and startup of a
self-contained tray host were not measured.

### Python

**Documented facts.** Python 3.13 and 3.14 are in bugfix status with
end-of-life in 2029-10 and 2030-10 respectively; 3.15 is scheduled for
2026-10-01 [S10]. asyncio streams provide `open_connection`, `readline`,
and `readuntil` for newline-delimited TCP [S12]. FastAPI documents
WebSocket endpoints, requiring the `websockets` package [S13].
`psutil.net_connections()` documents root requirements on macOS and AIX and
partial results on Linux without root, and records no privilege requirement
for Windows; `Process.connections()` is deprecated in favour of
`Process.net_connections()` [S14]. The python.org Windows embeddable
distribution is a ZIP intended to act as part of another application; it
excludes pip, Tcl/Tk, and documentation, and third-party packages must be
installed by the application installer or vendored [S11]. PyInstaller's
maintainers state that antivirus false positives on one-file executables
are common enough to have a dedicated label, attribute them to the
similarity of one-file bootloaders, and recommend reporting to the vendor
and avoiding one-file mode; the official troubleshooting page does not
cover the topic [S15]. Playwright has an official Python binding [S33].

**Inference.** Language fit is the strongest of all candidates. The
packaging route that best matches the maintainers' advice is a one-folder
PyInstaller build or the embeddable distribution wrapped by Inno Setup, not
a one-file executable. `psutil` is a third-party native dependency where
.NET uses the base library. Whether Nuitka reduces false positives was not
verified against a primary source.

### Node.js / TypeScript, Bun, and Deno

**Documented facts.** Node.js 22 is in Maintenance LTS until 2027-04-30;
Node.js 24 is Active LTS, enters Maintenance on 2026-10-20, and ends on
2028-04-30; Node.js 26 is Current, becomes LTS on 2026-10-28, and ends on
2029-04-30 [S16]. `node:sqlite` is at Stability 1.2, Release Candidate, in
both Node 24 (since v24.15.0) and Node 26 (since v25.7.0), having been
unflagged experimental since v22.13.0 and v23.4.0 [S17]. Single Executable
Applications are at Stability 1.1, Active development, in Node 24 and Node
26; an unsigned binary remains runnable on Windows and signing with
`signtool` is optional [S18]. Bun compiles standalone Windows x64 and arm64
executables and cross-compiles from other platforms, with Windows icon and
console flags available only when compiling on Windows [S19]. Deno compiles
standalone Windows executables, cross-compiles to Windows targets, and
documents a `signtool` signing step [S20]. Playwright officially supports
JavaScript and TypeScript [S33].

**Inference.** Node core offers no process-instance or listener enumeration,
so Same Session proof would depend on a native addon or on parsing the
output of Windows command-line tools, both of which add supply-chain or
parsing risk. Bun and Deno resolve the packaging question more cleanly than
Node SEA but not the enumeration gap.

### Go

**Documented facts.** Each Go major release is supported until two newer
majors ship; majors ship every six months in February and August, and Go
1.27 was released on 2026-08-19 [S21]. `gorilla/websocket` is actively
maintained, BSD-2-Clause, and passes the Autobahn suite [S22].
`modernc.org/sqlite` is a CGo-free SQLite port licensed BSD-3-Clause with
Windows amd64 listed as supported [S23]. Playwright has no official Go
binding [S33].

**Inference.** Packaging and dependency surface are the simplest of the
compiled options, but the roughly annual upgrade cadence, a new language for
the maintainer, and a second toolchain for browser tests are real costs.
Windows process and listener enumeration would rely on
`golang.org/x/sys/windows` or third-party packages; no single documented
high-level helper was identified.

### Rust

**Documented facts.** Stable Rust releases every six weeks through the
nightly, beta, and stable train [S24]. Playwright has no official Rust
binding [S33].

**Inference.** Comparable to Go for packaging and storage (`rusqlite`,
axum, tokio-tungstenite are mature but were not individually cited), with
the steepest learning curve relative to the maintainer's background. No
primary source quantifies learning curves.

### Lua-based options (Luvit, OpenResty)

**Documented facts.** OpenResty's Windows README states that I/O Completion
Ports are not supported and that the build should not be used for
production environments with very high concurrency levels; it positions the
Windows build as a development convenience [S25]. Luvit's install page
provides a PowerShell bootstrap script for Windows and lists prebuilt
Windows x64 binaries as still to be published [S26].

**Assessment.** Neither is a viable TowerGlance Host runtime on Windows for
v1. This does not exclude Lua as an embedded scripting layer inside another
host, which was outside this ticket's scope.

### Electron and Tauri shells

**Assessment.** The v1 boundary requires normal browser windows on the same
device [R1]. A shell that renders the UI itself violates that boundary. Used
only as a host-process wrapper, Electron ships a full browser engine and
Tauri a webview runtime solely to run a background process, which a native
tray process in any other stack does without that weight (inference; no
scenario was identified in which a shell wins).

## Engineering inference

**Ranked shortlist**, most to least viable for v1 (engineering inference,
not a decision):

1. **.NET (C#) + ASP.NET Core/Kestrel WebSockets + Microsoft.Data.Sqlite +
   Inno Setup + per-user tray process.** The only candidate whose base
   library covers process and listener enumeration, whose realtime server
   is documented under Native AOT, whose SQLite provider is first-party,
   which has an official Playwright binding, and whose runtime carries a
   multi-year LTS window [S1][S3][S5][S6][S7][S33].
2. **Python + FastAPI/Starlette WebSockets + stdlib sqlite3 + PyInstaller
   one-folder or the embeddable distribution + Inno Setup + tray process.**
   Best language fit and complete facilities, with a third-party native
   dependency for enumeration and a packaging route that must follow the
   PyInstaller maintainers' one-folder advice to limit antivirus false
   positives [S11][S14][S15].
3. **Go + net/http + gorilla/websocket + modernc.org/sqlite + static
   binary.** Simplest artefact, but a new language, an annual upgrade
   cadence, and a second toolchain for browser tests [S21][S22][S23][S33].
4. **Node.js/TypeScript or Bun/Deno + ws or Fastify + node:sqlite.** Shared
   language with the frontend, but no core process or listener enumeration,
   a Release Candidate storage API, and an SEA feature still in active
   development in the LTS line [S17][S18].

**Decisive trade-offs between the top two.** .NET wins on built-in
Same Session evidence APIs, packaging without a third-party bundler, static
typing, and LTS runway. Python wins on the maintainer's day-to-day fluency
and on stdlib coverage for TCP and SQLite. The packaging-trust gap is
narrower than a first reading suggests: the SmartScreen and Smart App
Control friction is identical for both until signing exists [S35], and the
PyInstaller-specific antivirus risk is mitigated by the maintainers' own
one-folder advice [S15]. The remaining difference is therefore mostly a
maintainer preference between fluency now and a typed, batteries-included
host for a project that must run unattended beside a game.

**Transport.** WebSocket-first is the evidence-backed choice because of the
documented `EventSource` connection cap over HTTP/1.1 with several open
windows [S27]; SSE remains acceptable only for a single-window fallback.

## Decision inputs for the architecture choice

- Confirm whether the maintainer can obtain an Azure Artifact Signing
  Public Trust identity (individuals: United States or Canada only) or must
  plan for an organization certificate or an unsigned first release with
  documented SmartScreen and Smart App Control friction [S35][S36].
- Choose between a per-user tray process (no administrator step) and a
  Windows Service (administrator installation) for host lifetime [S8][S9].
- If .NET: choose raw WebSockets under Native AOT, or SignalR under a
  trimmed non-AOT publish, in light of the documented limitations [S3][S4].
- If Python: choose the one-folder PyInstaller or embeddable-distribution
  route, and accept `psutil` as a native dependency [S11][S14][S15].
- Record WebSocket as the browser transport and `sessionStorage` or URL
  state as the per-window store [S27][S30].
- Confirm the installer tool's licence directly: Inno Setup is free for
  commercial use, WiX's maintenance fee exempts non-revenue use, and
  Velopack is MIT [S37][S38][S39].
- Plan the WCAG 2.2 stripboard interaction so every drag has a single-pointer
  alternative and targets meet the 24-pixel minimum [S32].

## Uncertainties

- No first-party Microsoft document was found stating that a
  `127.0.0.1`-bound listener never triggers the Windows Firewall prompt;
  this needs a prototype check.
- `GetActiveTcpListeners` and `Process.StartTime` document no privilege
  requirement, but unelevated behaviour on a representative Windows 11
  machine was not exercised here [S5][S6]. The same applies to
  `psutil.net_connections()` on Windows [S14].
- The residual antivirus false-positive rate for one-folder PyInstaller
  builds, the embeddable distribution, or Nuitka output was not measured;
  only the maintainers' qualitative statements are available [S15].
- Cleartext HTTP/2 as a way to lift the SSE connection cap on a loopback
  host was not verified against current browser behaviour.
- Chrome's stated plan to extend Local Network Access to cross-origin
  local requests could change the loopback boundary later [S31].
- Community Playwright wrappers for Go and Rust were not evaluated; only
  official bindings were counted [S33].
- Self-contained .NET tray-host size and startup, and PyInstaller
  one-folder startup, were not measured.

## Next steps

Inputs for issue #14, not directives:

1. Resolve the signing route before choosing the installer, because it
   determines first-run friction for every stack [S35][S36].
2. If .NET is chosen, prototype a minimal Kestrel WebSocket host that reads
   `GetActiveTcpListeners` and the game process start time unelevated on
   Windows 11 and confirm the firewall does not prompt for a loopback bind.
3. If Python is chosen, prototype a one-folder PyInstaller or embeddable
   build with `psutil` and measure Defender and one consumer antivirus
   result directly.
4. Record the WebSocket transport, per-window `sessionStorage` policy, and
   the three WCAG 2.2 criteria as fixed inputs to the experience prototypes.

## Sources

### Runtime, framework, and library documentation

- **S1 — .NET support policy:** [.NET and .NET Core official support policy](https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core)
  — LTS and STS durations; .NET 8, 9, and 10 release and end-of-support
  dates. Accessed 2026-09-09.
- **S2 — .NET application publishing overview:** [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/deploying/)
  — framework-dependent versus self-contained, single-file, trimming,
  ReadyToRun, and Native AOT publishing. Accessed 2026-09-09.
- **S3 — ASP.NET Core support for Native AOT:** [Microsoft Learn](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/native-aot)
  — feature compatibility table (WebSockets supported; Minimal APIs and
  SignalR partial; MVC and Blazor Server unsupported). Accessed
  2026-09-09.
- **S4 — What's new in ASP.NET Core in .NET 9:** [Microsoft Learn](https://learn.microsoft.com/en-us/aspnet/core/release-notes/aspnetcore-9.0)
  — SignalR trimming and Native AOT limitations (strongly typed hubs;
  value-type `IAsyncEnumerable<T>` and `ChannelReader<T>`). Accessed
  2026-09-09.
- **S5 — `IPGlobalProperties.GetActiveTcpListeners`:** [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/api/system.net.networkinformation.ipglobalproperties.getactivetcplisteners)
  — local TCP listener enumeration via `GetTcpTable`; no privilege note.
  Accessed 2026-09-09.
- **S6 — `Process.StartTime`:** [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.process.starttime)
  — process start time for local processes. Accessed 2026-09-09.
- **S7 — Microsoft.Data.Sqlite overview:** [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/standard/data/sqlite/)
  — first-party ADO.NET SQLite provider. Accessed 2026-09-09.
- **S8 — Create Windows Service using BackgroundService:** [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/extensions/windows-service)
  — `Microsoft.Extensions.Hosting.WindowsServices`, single-file publish,
  administrator requirement for `sc.exe create`. Accessed 2026-09-09.
- **S9 — `NotifyIcon` class:** [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.notifyicon)
  — notification-area icon component. Accessed 2026-09-09.
- **S10 — Python supported versions:** [Python Developer's Guide](https://devguide.python.org/versions/)
  — 3.13 and 3.14 status and end-of-life; 3.15 schedule. Accessed
  2026-09-09.
- **S11 — Using Python on Windows, embeddable package:** [docs.python.org](https://docs.python.org/3/using/windows.html)
  — purpose, contents, and exclusions of the embeddable distribution.
  Accessed 2026-09-09.
- **S12 — asyncio streams:** [docs.python.org](https://docs.python.org/3/library/asyncio-stream.html)
  — `open_connection`, `readline`, `readuntil`. Accessed 2026-09-09.
- **S13 — FastAPI WebSockets:** [fastapi.tiangolo.com](https://fastapi.tiangolo.com/advanced/websockets/)
  — WebSocket endpoints and the `websockets` dependency. Accessed
  2026-09-09.
- **S14 — psutil API reference:** [psutil.io](https://psutil.io/api/)
  — `net_connections()` privilege notes per platform; deprecation of
  `Process.connections()`. Accessed 2026-09-09.
- **S15 — PyInstaller maintainers on antivirus false positives:** [Discussion #5877](https://github.com/orgs/pyinstaller/discussions/5877)
  — maintainer statements on cause, label, vendor reporting, and one-folder
  advice; the official [troubleshooting page](https://pyinstaller.org/en/stable/when-things-go-wrong.html)
  does not cover antivirus. Accessed 2026-09-09.
- **S16 — Node.js release schedule:** [nodejs/Release](https://github.com/nodejs/Release)
  — Node 22, 24, and 26 LTS and end-of-life dates. Accessed 2026-09-09.
- **S17 — `node:sqlite`:** [Node 24 documentation](https://nodejs.org/docs/latest-v24.x/api/sqlite.html)
  and [Node 26 documentation](https://nodejs.org/api/sqlite.html)
  — Stability 1.2 Release Candidate and history. Accessed 2026-09-09.
- **S18 — Node single executable applications:** [Node 24 documentation](https://nodejs.org/docs/latest-v24.x/api/single-executable-applications.html)
  — Stability 1.1 Active development; optional Windows signing. Accessed
  2026-09-09.
- **S19 — Bun standalone executables:** [bun.sh](https://bun.sh/docs/bundler/executables)
  — Windows targets, cross-compilation, Windows-only flags. Accessed
  2026-09-09.
- **S20 — `deno compile`:** [docs.deno.com](https://docs.deno.com/runtime/reference/cli/compile/)
  — Windows targets, cross-compilation, `signtool` workflow. Accessed
  2026-09-09.
- **S21 — Go release history and cycle:** [go.dev release history](https://go.dev/doc/devel/release)
  and [Go release cycle](https://go.dev/wiki/Go-Release-Cycle)
  — two-newer-majors support policy, six-month cadence, Go 1.27 date.
  Accessed 2026-09-09.
- **S22 — gorilla/websocket:** [GitHub](https://github.com/gorilla/websocket)
  — maintenance status, BSD-2-Clause, Autobahn compliance. Accessed
  2026-09-09.
- **S23 — modernc.org/sqlite:** [pkg.go.dev](https://pkg.go.dev/modernc.org/sqlite)
  — CGo-free port, BSD-3-Clause, Windows amd64 support. Accessed
  2026-09-09.
- **S24 — Rust release channels:** [The Rust Programming Language, Appendix G](https://doc.rust-lang.org/book/appendix-07-nightly-rust.html)
  — six-week stable release train. Accessed 2026-09-09.
- **S25 — OpenResty Windows README:** [GitHub](https://github.com/openresty/openresty/blob/master/doc/README-windows.md)
  — IOCP unsupported; not for high-concurrency production. Accessed
  2026-09-09.
- **S26 — Luvit installation:** [luvit.io](https://luvit.io/install.html)
  — Windows PowerShell bootstrap; prebuilt Windows binaries unpublished.
  Accessed 2026-09-09.

### Web platform and accessibility

- **S27 — MDN `EventSource`:** [developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/EventSource)
  — six-connection limit per browser and domain without HTTP/2. Accessed
  2026-09-09.
- **S28 — MDN WebSocket API:** [developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)
  — full-duplex message transport. Accessed 2026-09-09.
- **S29 — W3C Secure Contexts:** [w3.org](https://www.w3.org/TR/secure-contexts/)
  — loopback and `localhost` as potentially trustworthy origins. Accessed
  2026-09-09.
- **S30 — HTML Standard, Web storage:** [html.spec.whatwg.org](https://html.spec.whatwg.org/multipage/webstorage.html)
  — `sessionStorage` per top-level browsing context; `localStorage` shared.
  Accessed 2026-09-09.
- **S31 — Chrome Local Network Access:** [developer.chrome.com](https://developer.chrome.com/blog/local-network-access)
  — public-to-local request scope; Chrome 142 launch; planned extension.
  Accessed 2026-09-09.
- **S32 — WCAG 2.2:** [W3C Recommendation](https://www.w3.org/TR/WCAG22/)
  and [W3C news, 2023-10-05](https://www.w3.org/WAI/news/2023-10-05/wcag22rec)
  — status and dates; text and level of 2.5.7, 2.5.8, and 2.4.11. Accessed
  2026-09-09.
- **S33 — Playwright supported languages:** [playwright.dev](https://playwright.dev/docs/languages)
  — JavaScript/TypeScript, Python, Java, and .NET only. Accessed
  2026-09-09.

### Windows publishing

- **S34 — Sign an MSIX package:** [Microsoft Learn](https://learn.microsoft.com/en-us/windows/msix/package/signing-package-overview)
  — signing requirement, trusted chain, signing options and costs, Artifact
  Signing eligibility summary. Accessed 2026-09-09.
- **S35 — SmartScreen reputation for Windows app developers:** [Microsoft Learn](https://learn.microsoft.com/en-us/windows/apps/package-and-deploy/smartscreen-reputation)
  — reputation model, unsigned and signed behaviour, EV note, Smart App
  Control, Artifact Signing pricing. Accessed 2026-09-09.
- **S36 — Azure Artifact Signing quickstart:** [Microsoft Learn](https://learn.microsoft.com/en-us/azure/artifact-signing/quickstart)
  — Public Trust eligibility by country for organizations and individuals.
  Accessed 2026-09-09.
- **S37 — Inno Setup license:** [jrsoftware.org](https://jrsoftware.org/files/is/license.txt)
  — free use for any purpose including commercial. Accessed 2026-09-09.
- **S38 — WiX Toolset license and maintenance fee:** [LICENSE.TXT](https://github.com/wixtoolset/wix/blob/main/LICENSE.TXT)
  and [OSMFEULA.txt](https://github.com/wixtoolset/wix/blob/main/OSMFEULA.txt)
  — Microsoft Reciprocal License; fee only for revenue-generating users at
  or above ten thousand US dollars annual gross revenue. Accessed
  2026-09-09.
- **S39 — Velopack:** [GitHub](https://github.com/velopack/velopack)
  — MIT license; platform and language support. Accessed 2026-09-09.

### Repository constraint sources

Cited as the origin of the constraints compared; not re-verified here.

- **R1 —** [Map the TowerGlance v1 decision route](https://github.com/CrankyAnt/TowerGlance/issues/1)
  (standing v1 boundaries and delivery strategy).
- **R2 —** [Establish v1-relevant Communication Port data semantics](https://github.com/CrankyAnt/TowerGlance/issues/27#issuecomment-5234586453).
- **R3 —** [Prototype a bounded Communication Port handshake and read](https://github.com/CrankyAnt/TowerGlance/issues/26#issuecomment-5172277290).
- **R4 —** [Prototype Operational Session continuity across Communication Port disruption](https://github.com/CrankyAnt/TowerGlance/issues/29#issuecomment-5245600686).
- **R5 —** [Define the stripboard domain and mode contract](https://github.com/CrankyAnt/TowerGlance/issues/10#issuecomment-5593156288).
- **R6 —** [Define the browser state and reconnect contract](https://github.com/CrankyAnt/TowerGlance/issues/11#issuecomment-5594812498).
- **R7 —** [TS3 data-interface inventory](ts3-data-interface-inventory.md)
  (loopback listener, framing signature, bidirectionality).
