# link-hardware

![Link-LR with the cover off: base board, LoRa module plugged on top, 18650 cell](https://raw.githubusercontent.com/semtronik/link/main/link-lr/images/link-lr_interieur.jpg)

Hardware design sources for the **Link** platform: a common ESP32-S3 base
board paired with interchangeable interface modules, in a shared MJF
enclosure.

| Product | Interface | Status |
|---|---|---|
| **Link-LR** | LoRa P2P, 868/915 MHz | first product |
| **Link-485** | RS485 | planned |

> **Current revisions** — base board `0.2.0`, LoRa module `0.2.0`,
> enclosure `0.1.0`. Both boards are validated on the bench; the enclosure
> sources are those of the first print.

This repository holds the design sources. The desktop application lives in
[`link-studio`](https://github.com/semtronik/link-studio), the device firmware
in [`link-firmware`](https://github.com/semtronik/link-firmware), and the
product overview and device↔app protocol in
[`link`](https://github.com/semtronik/link).

## Layout

```
board/
├── link-base-brd/      base board: MCU, display, power
└── link-mod-lr-brd/    LoRa interface module
mechanical/             enclosure
```

Each artifact folder is self-contained — sources, its own `.gitignore`, and
its `outputs/` — so it could be extracted into a standalone repository
without losing anything.

Top-level folders name a **discipline**, not an artifact type: one discipline
can produce several types (`mechanical/` will also hold the front faces).
Folder names are free to change; the tokens in file names are not.

## Setup

Boards reference a shared component library kept in a separate
repository, [`semtronik-kicad-libs`](https://github.com/semtronik/semtronik-kicad-libs).

1. Clone it next to this repository.
2. In KiCad, go to **Preferences → Configure Paths** and add an
   environment variable `SEMTRONIK_KICAD_LIBS` pointing to your local
   clone.

## Reading a file name

Every file is fully qualified, so it can be sent to a fab or a printer on its
own and still say what it is:

```
link-<artifact>[-<variant>]-<type>
```

| Token | Meaning |
|---|---|
| `-brd` | Printed circuit board (KiCad project) |
| `-case` | Enclosure (Fusion 360) |
| `-face` | PET front face (artwork + cutting) |

So `link-mod-lr-brd` is the LoRa interface module board, and
`link-case-top-v0.1.0.step` is the top half of version 0.1.0 of the
enclosure.

## Versions

Hardware follows semantic versioning, with hardware semantics:

- **MAJOR** — breaking: base↔module connector pinout, mounting or dimensions
  changed. A `1.x` module no longer fits a `2.x` base.
- **MINOR** — compatible evolution: component added, protection improved,
  layout reworked. All interfaces stay valid.
- **PATCH** — no functional change: silkscreen fix, component value, routing
  defect.

`0.x` means **pre-production**: the design is under validation and breaking
changes happen without a MAJOR bump. `1.0.0` is the revision customers
physically own — from then on, compatibility is a contract.

Each artifact is versioned independently: the base board can be at `1.0.0`
while a module is still at `0.2.0`.

The version of a physical board is printed on its **silkscreen** — that is
what to quote when reporting an issue. It also appears in the schematic
title block and in the file names under `outputs/`.

## Outputs and releases

Each artifact folder carries an `outputs/` directory holding the fab-agnostic
exports of its **last freeze**: schematic PDF, BOM, position file, gerbers and
STEP.

They are generated from the KiCad jobset committed next to the sources
(`outputs.kicad_jobset`): open the project, run the jobset, and every output
is produced and named in one click. Output names are templated as
`${PROJECTNAME}-v${REVISION}-...`, so the version comes from the schematic
title block and is never typed in — an export cannot carry a version the
source does not state.

`main` only ever contains revisions that were manufactured **and** physically
validated, so what is in `outputs/` on `main` always describes the sources
next to it. Work in progress lives on branches, where that guarantee does not
hold.

Published releases carry the same files, attached to the freeze tag:

```
link-base-brd/v0.2.0
link-mod-lr-brd/v0.2.0
```

Fab-branded exports (vendor plugin folders) are never committed and never
released: releases stay fab-agnostic.

## License

Licensed under the **CERN-OHL-S v2** (strongly reciprocal). You may study,
modify, manufacture and sell hardware based on these sources — provided any
modified design is published under the same licence. See `LICENSE`.

Products made from these sources must keep the source location visible on the
external case where practicable (CERN-OHL-S v2 §4).
