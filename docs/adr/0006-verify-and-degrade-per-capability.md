---
status: accepted
---

# Support is verified and degraded per capability, with no bundled replacement data

Installed official content varies, and one missing source must not take down
what still works. Verification is capability-specific: each capability starts
`unverified` for each content class and becomes `supported` or `unsupported` only through the
validated method, newly installed or changed local content starts `unverified`
and is verified locally, and a stored result is invalidated only when the
content or the verification standard changes; only a `supported` capability is
`available` or `unavailable`, and a missing, damaged or unreadable required
source makes only that capability or layer `unavailable` while independently
usable static, planned, live and diagnostic layers stay available. The
evidence is in
[Define the verified v1 compatibility baseline](https://github.com/CrankyAnt/TowerGlance/issues/5#issuecomment-5159917722),
[Decide whether supplemental local data is needed](https://github.com/CrankyAnt/TowerGlance/issues/6#issuecomment-5156535468)
and [Define Operational Session identity, source authority, and recovery](https://github.com/CrankyAnt/TowerGlance/issues/7#issuecomment-5244333107).

## Considered options

- **A fixed list of supported airports:** official content changes with every
  installed or updated package, and a list would either overclaim or turn away
  content that conforms to the validated standard.
- **The whole airport or session unsupported when one layer is missing:**
  usable layers, such as the Traffic Schedule or Manual Stripboard Mode, would
  be lost for one gap.
- **Bundled supplemental airport, schedule or geometry data:** the installed
  official baseline has `.airport` geometry for all 29 of its 29 airport
  packages, and game-derived data stays authoritative; TowerGlance needs no
  hosted dataset or mandatory runtime download.

## Consequences

- Insufficient evidence stays `unverified`; it is never guessed to be
  `unsupported` or `unavailable`.
- A verified `unsupported` result is reserved for content outside the v1
  scope; missing or unreadable official content is `unavailable` through
  Source Health.
- Normal event silence does not degrade Source Health.
- A directory with schedule or profile data but no installed airport package is
  no airport candidate and no coverage gap.
