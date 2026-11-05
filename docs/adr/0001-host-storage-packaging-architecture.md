---
status: accepted
---

# Single .NET tray host serving a React browser UI over loopback, with file persistence and Velopack distribution

TowerGlance v1 needs a local Windows host that follows one Tower! Simulator 3
Operational Session independently of any browser window and installs without
a developer toolchain or administrator rights. It runs as one per-user .NET
tray process, not a Windows Service, that runs one adapter per game source, writes
to the game only through a dedicated Game-backed Strip Position writer with no
generic command API, serves an embedded React UI over a loopback-only
HTTP/WebSocket endpoint, keeps a few plain files instead of a database, and is
packaged and updated with Velopack from GitHub Releases. The evidence is in
[Compare viable local host and browser stacks](https://github.com/CrankyAnt/TowerGlance/issues/13#issuecomment-5604647050)
and [the stack comparison on the research branch](https://github.com/CrankyAnt/TowerGlance/blob/research/13-host-browser-stack-comparison/docs/research/host-browser-stack-comparison.md);
the reasoning is in
[Choose the v1 host, storage, and packaging architecture](https://github.com/CrankyAnt/TowerGlance/issues/14).

## Considered options

- **Python host** (asyncio, FastAPI/Starlette): the closest language fit,
  but a third-party native dependency for process and listener
  enumeration, a bundler-based packaging route with documented antivirus
  false positives for one-file builds, and weaker static typing. Go and
  Node/Bun/Deno ranked lower on language fit and built-in enumeration.
- **Windows Service:** independent of login but needs administrator
  installation and separates the host from the interactive session in which
  the game runs.
- **SQLite:** transactional storage and queries for data that is one
  preference, one bounded snapshot, and rolling logs; rejected as
  unnecessary weight.
- **Inno Setup installer with manual updates:** free and simple, but no
  update mechanism; superseded by Velopack, because v1 updates itself
  automatically.
- **Microsoft Store (MSIX):** Microsoft signing and updates without
  SmartScreen warnings, at the cost of MSIX packaging constraints and
  per-release certification.
- **Svelte, Lit, or vanilla TypeScript frontend:** viable, but React offers
  the widest set of accessible primitives and keyboard-capable drag
  alternatives needed for WCAG 2.2 criteria 2.5.7 and 2.5.8.
- **Native window shell instead of browser windows** (Electron, Tauri, or a
  .NET WebView2 shell): the React UI would be identical, so a shell adds
  window management, multi-monitor placement, zoom, and menus as TowerGlance's own
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
