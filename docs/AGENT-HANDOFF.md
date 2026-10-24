# Agent handoff

Copy this prompt into your local AI agent, replacing the requested scope:

> Help me use a custom launcher on my Samsung Tizen device. Read README.md and docs/NATIVE-HOME.md. My requested scope is: [install only / Home button / Home button and startup]. Discover current model, firmware, architecture, runtime, network addresses, execution identity and installed launcher ID. Do not reuse reference-device values. Review third-party source before executing it. Make a private per-device record and immutable baseline. Proceed with necessary authorized preparation without repeatedly asking permission. Before native writes, establish compatibility, prepare a concrete exact-value restore procedure, show the proposed changes and obtain any authorization still required. Preserve signing identity and a visible Samsung Home escape. Separate installation, Home routing and startup. Require checked native readback and my physical confirmation. On disconnection or unknown state, query before retrying writes. Keep credentials out of chat/logs and report unknowns honestly.

## Keep the public repository clean

Store private device records, originals, logs, compiled helpers, widgets and signing files under `.local/` or another excluded location. Commit only sanitized documentation and templates. Check staged files before publishing. `.gitignore` is a convenience, not proof that a file contains no private data.

## Suggested result record

- Device/model/firmware evidence recorded privately.
- Installation verified: yes/no, app/version.
- Original baseline captured: yes/no, private path.
- Exact restore verified: yes/no.
- Physical stock Home after restore: yes/no.
- Physical custom Home: yes/no.
- Startup native readback: yes/no.
- Normal off/on: yes/no.
- Full cold boot: yes/no/not tested.
- Firmware-change recovery: reviewed/unknown.
- Final applied state and restore command recorded privately.
