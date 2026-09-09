---
status: accepted
date: 2026-09-09
---

# Single .NET tray host serving a React browser UI over loopback, with file persistence and Velopack distribution

TowerGlance v1 needs a local Windows host that follows one Tower! Simulator 3
Operational Session, keeps running independently of any browser window, and
installs without a developer toolchain. We chose one .NET (C#) tray process
that hosts the source adapters, the normalized state, and a loopback-only
HTTP/WebSocket endpoint serving an embedded React UI, persists only a small
set of plain files, and is packaged and updated with Velopack from GitHub
Releases. The evidence and ranked comparison are recorded in
[Compare viable local host and browser stacks](https://github.com/CrankyAnt/TowerGlance/issues/13#issuecomment-5604647050)
and `docs/research/host-browser-stack-comparison.md`; the decision was made in
[Choose the v1 host, storage, and packaging architecture](https://github.com/CrankyAnt/TowerGlance/issues/14).

## Decision

- **Host stack:** .NET (current LTS) with ASP.NET Core / Kestrel. Raw
  WebSockets with an explicit message envelope rather than SignalR, so the
  baseline-plus-ordered-updates, resynchronization, and no-replay rules from
  the browser state contract are implemented visibly. Published
  self-contained for `win-x64`; no Native AOT, because the notification-area
  icon uses Windows Forms `NotifyIcon`.
- **Process shape:** one per-user tray process, single instance per
  interactive user session, installed without administrator rights. No
  Windows Service. Start at login is an optional setting, off by default.
- **Source boundary:** one adapter per source (Communication Port, static
  game files, Traffic Schedule Source resolver, supplementary Player.log),
  each producing Normalized Facts with provenance and its own Source Health,
  and isolating its own parse failures. The Communication Port adapter
  exposes no generic command API; the only write path is a dedicated Strip
  Position Writer that implements acknowledgement, exact STRIPS readback,
  in-flight tracking, and idempotency. A Session Continuity component
  applies the Same, Different, and Ambiguous rules from process-instance
  identity, listener presence, Ready context, and joined live evidence.
- **Game discovery:** the configured Communication Port, or a probe of the
  fixed port set the game offers, connecting only when a loopback listener
  and the game process are present; the installed game directory from the
  Steam registry and library folders, verified by the game executable, with
  a user-selected folder as override.
- **Browser delivery:** React + TypeScript + Vite frontend, built at
  development time and embedded in the host, served over plain HTTP on
  `127.0.0.1` only (Local Access; loopback is a secure context). One
  WebSocket per window carries the baseline, sequence-numbered updates,
  resynchronization, and commands with client-generated idempotency keys.
  Per-window navigation, zoom, and filters live in `sessionStorage` or the
  URL. The host's own port has a fixed default with fallback to the next
  free port; the tray menu and Start Menu entry open the actual URL in the
  default browser.
- **Persistence:** plain files under `%LOCALAPPDATA%\TowerGlance`: JSON
  settings (Stripboard Mode preference, ports, game folder override), one
  Recovery Snapshot file replaced atomically by write-and-rename, and
  rolling log files within the limits of the diagnostics contracts.
  Operational state stays in memory. No database.
- **Diagnostics separation:** the development inspection instrument and
  tracing are compiled only into a Development build configuration; release
  builds carry the compact error log only.
- **Distribution and updates:** Velopack produces the per-user installer,
  full and delta packages, and the release index, published to GitHub
  Releases; the host checks GitHub Releases for updates through Velopack's
  GitHub source, with a setting to disable the check. The installer is
  unsigned for v1.

## Considered options

- **Python host** (asyncio, FastAPI/Starlette): best language fit for the
  maintainer, but a third-party native dependency for process and listener
  enumeration, a bundler-based packaging route with documented antivirus
  false positives for one-file builds, and weaker static typing. Go and
  Node/Bun/Deno ranked lower on maintainer fit and built-in enumeration.
- **Windows Service:** independent of login but needs administrator
  installation and separates the host from the interactive session in which
  the game runs.
- **SQLite:** transactional storage and queries for data that is one
  preference, one bounded snapshot, and rolling logs; rejected as
  unnecessary weight.
- **Inno Setup installer with manual updates:** free and simple, but no
  update mechanism; superseded by Velopack once automatic updates were
  chosen.
- **Microsoft Store (MSIX):** Microsoft signing and updates without
  SmartScreen warnings, at the cost of MSIX packaging constraints and
  per-release certification.
- **Svelte, Lit, or vanilla TypeScript frontend:** viable, but React offers
  the widest set of accessible primitives and keyboard-capable drag
  alternatives needed for WCAG 2.2 criteria 2.5.7 and 2.5.8.
- **Native window shell instead of browser windows** (Electron, Tauri, or a
  .NET WebView2 shell): the React UI would be identical, so a shell adds
  window management, multi-monitor placement, zoom, and menus as our own
  code while removing what browsers provide for free and closing the door
  to later multi-device use. Electron would add a second runtime and stack
  beside the .NET host; Tauri requires Rust. Rejected for v1. If a
  native-feeling shell proves necessary, a .NET WebView2 shell hosting the
  same React app over the same loopback contract is the reversible path:
  the WebView2 Evergreen Runtime is included in Windows 11 and Playwright
  can drive WebView2 through the Chrome DevTools Protocol.

## Consequences

- Runtime game data remains local; the update check is the host's only
  outbound network use and can be switched off.
- Unsigned releases show a SmartScreen warning on first run and may be
  blocked by Smart App Control until a signing route exists; individual
  Azure Artifact Signing is unavailable outside the United States and
  Canada, so signing is revisited after v1.
- The frontend toolchain (Node.js, Vite) is a development dependency only;
  users install nothing beyond the Velopack installer.
- Unelevated listener enumeration and the absence of a firewall prompt for a
  loopback-only bind are documented but not yet exercised; the first host
  prototype must confirm them.
- The loopback endpoint is reachable by every local process and web page;
  WebSocket upgrades are accepted only with a loopback `Origin` and a
  per-install session token, and the host never exposes a generic command
  API.
- The two-part feel of a tray host plus a browser tab is mitigated by
  opening the browser on host start, a Start Menu entry that opens the
  current URL, and tray status. Whether Edge and Chrome allow installing the
  UI as an app from the loopback origin is verified by the integrated
  experience prototype, not assumed.
