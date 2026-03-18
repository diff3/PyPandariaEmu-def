# PyPandariaEmu Data

Runtime data repository for `PyPandariaEmu`.

This repo contains packet definitions, decoded reference JSON, promoted raw/debug packets and live captures. It does not contain server logic or proxy code.

## Layout

```text
data/
├── captures/
│   ├── debug/
│   ├── json/
│   └── focus/
│       ├── debug/
│       └── json/
├── debug/
├── def/
└── json/
```

## Directories

### `def/`

DSL packet definitions.

- one packet per file
- file name matches opcode name
- example: `CMSG_AUTH_SESSION.def`

These are the files used by the DSL runtime for decode and encode.

### `json/`

Promoted decoded reference payloads.

- one file per opcode
- usually created as `{}` first during promote
- later filled with expected decoded structure if needed

Example:

- `json/CMSG_AUTH_SESSION.json`

### `debug/`

Promoted raw/debug packet artifacts.

- one file per opcode
- contains raw packet metadata, header data, hex dumps and debug-friendly fields

Example:

- `debug/CMSG_AUTH_SESSION.json`

## Captures

`captures/` holds live packet dumps created during runtime observation.

### `captures/json/`

Latest decoded packet per opcode.

- overwritten on each new dump for the same opcode
- intended for quick inspection of the latest seen packet

### `captures/debug/`

Latest raw/debug packet per opcode.

- overwritten on each new dump for the same opcode
- contains raw packet metadata and hex output

### `captures/focus/json/`

Decoded focus captures.

- stores multiple packets
- filenames include opcode, unix time and id

Example:

- `CMSG_AUTH_SESSION_1777777777_0001.json`

### `captures/focus/debug/`

Raw/debug focus captures.

- same naming scheme as focus `json/`
- used when you want to keep many copies of the same opcode instead of overwriting the latest one

## Promote / Demote Flow

Promote copies packet artifacts from `captures/` into the stable runtime folders:

- `captures/debug/<OPCODE>.json` -> `debug/<OPCODE>.json`
- creates `def/<OPCODE>.def` with `{}`
- creates `json/<OPCODE>.json` with `{}`

For focus captures, promote strips the timestamp/id suffix and promotes by opcode name.

Demote removes the promoted files again from:

- `def/`
- `json/`
- `debug/`

## Sync

`protocol sync` updates already promoted `debug/` and `json/` files from the latest matching capture files in `captures/`.

It does not rewrite `.def` files.

## Naming Rules

- opcode name is the canonical identity
- promoted files use plain opcode name only
- focus captures append `_unixtime_id`

## Notes

- `captures/` is transient runtime data
- `def/`, `json/` and `debug/` are the stable promoted artifact set
- `__init__.py` in `def/` is not a packet definition and should be ignored by packet lookup
