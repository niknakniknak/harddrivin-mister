# Hard Drivin' for MiSTer FPGA: CRT 480i SD build

> **This is a separate 15 kHz build** ("SD": standard definition), for low-res CRTs: TVs, PVMs and 15 kHz arcade
> monitors. The original core is [Fulviuus's](https://github.com/retrogarage/harddrivin-mister); use that one on multi-sync or
> 31 kHz VGA monitors.
>
> - **Analog output (VGA, and JAMMA adapters): 15 kHz 480i only.** Every line of the
>   game's 508x384 picture, interlaced, locked to the game's own frame rate. There is no
>   path for the original 25 kHz picture, so no menu option or `MiSTer.ini` setting can
>   put 25 kHz on the monitor. Until the picture is locked (about a tenth of a second
>   after loading) the output is black with no sync.
> - **HDMI: the normal picture**, as on the original core.
> - **Menu:** a **CRT 480i** page with Deflicker, Height, H-Position and V-Position.
>
> **What's different in this build:**
> - The analog output carries only 15 kHz 480i. The controls and diagnostic overlays show on
>   HDMI only.
> - Built with Quartus Prime 17.0.2 Lite, the version MiSTer standardises on (fitter seed 2,
>   which meets timing). The original below targets Quartus Lite 24.1.
>
> **What you need for the CRT side:**
>
> | Item | Requirement |
> | --- | --- |
> | Monitor | A 15 kHz CRT that takes standard 480i (60 Hz interlaced): a TV, PVM or 15 kHz arcade monitor. Nearly all do. |
> | Connection | The MiSTer's analog output: VGA through an analog I/O board or an RGB cable, or a JAMMA adapter |
> | Sync | Set `composite_sync` in `MiSTer.ini` to suit the cable: most SCART cables and PVMs want `composite_sync=1` |
> | MiSTer.ini | Nothing else: the scaler and scandoubler settings don't reach the analog output in this build |
> | Board, SDRAM, ROMs | As for the original core (below) |
>
> **Tested on:** Sony PVM, Panasonic CRT TV, Wells-Gardner and Hantarex arcade monitors.
>
> **Disclaimer.** Every effort has been made to make this safe for 15 kHz monitors: the
> analog output has no path for the 25 kHz picture, the source is open for anyone to check,
> and it has been tested on several 15 kHz monitors. Even so, it is provided as is, without
> warranty of any kind, and you use it at your own risk: the authors can't be held
> responsible for damage to your monitor or other equipment.
>
> Games: Hard Drivin' (Cockpit, rev 7). The 480i stage is `crt_480i` by Chris Watson (GPL-2.0-or-later). All
> credit for the core itself goes to Fulviuus; the rest of this README is theirs, unchanged.

---

# Hard Drivin’ for MiSTer

FPGA implementation of Atari’s Hard Drivin’ arcade hardware for the
DE10-Nano/MiSTer. This release supports **Cockpit, revision 7** and runs the
original 68010, TMS34010, ADSP-2100, 68000 and TMS32010 programs in FPGA logic.

## Screenshots

![Hard Drivin’ title and track map running on the MiSTer core](screenshots/hard-drivin-title.jpeg)

![Hard Drivin’ champion screen](screenshots/hard-drivin-champion.jpeg)

![Hard Drivin’ cockpit view approaching the stunt-track loop](screenshots/hard-drivin-loop.jpeg)

## Features

- 68010 main processor, TMS34010 graphics and math processors, and ADSP-2100 geometry processor.
- Polygon road and scenery rendering with the cockpit dashboard.
- Driver sound board with 68000, TMS32010 and sample playback.
- Analog steering, gamepad pedals, wheel mode, and sequential or H-pattern gear selection.
- Persistent game settings and calibration through MiSTer NVRAM.
- MiSTer video output with original 4:3 and raw-pixel aspect options.

## Installation and ROM

Release files are in `releases/`:

| File | SHA-1 |
| --- | --- |
| `HardDrivin_cockpit.rbf` | `82190fe8a9ad72c8a77bfb13c784f16e72bc65c7` |
| `Hard Drivin' (Cockpit, rev 7).mra` | `8ece851e0b6af11f19e549c5cef8c0165ffae24f` |

Copy the RBF to MiSTer’s `_Arcade/cores` directory and the MRA to `_Arcade`.
Place the unmodified MAME **`harddriv.zip`** ROM set in a MiSTer arcade ROM
search directory. Game ROM archives are not included. SHA-256 hashes are also
provided in [`releases/SHA256SUMS`](releases/SHA256SUMS).

Uses stock MiSTer Main. Game settings are saved in MiSTer NVRAM.

Cabinet force feedback is not provided.

## Build and test

The Quartus project targets Quartus Lite 24.1. Required HDL dependencies are
included; no submodule checkout is needed:

```sh
make prepare ROM=path/to/harddriv.zip
make build
make check
make test
```

The preparation step extracts and checks the original cockpit power-up RAM
images. Generated files stay outside Git. Install `output_files/HardDrivin.rbf`
as `HardDrivin_cockpit.rbf` to use it with the supplied MRA.

Checks require Python 3.10+ and Tcl. The ROM-free memory regressions require
Verilator 5 and a C++ compiler.

## Source and licensing

Core development: [Fulviuus](https://github.com/Fulviuus).

Project integration is distributed under **GPL-3.0-or-later**. This repository
includes third-party FPGA components under their original licenses and
copyright notices:

- MiSTer framework and SDRAM controller: GPL-2.0 / GPL-2.0-or-later, as marked in each file.
- TMS34010: MIT.
- TG68K: LGPL-3.0-or-later.
- FX68K: GPL-3.0.
- IKA32010: BSD-2-Clause.

See [`LICENSE`](LICENSE), [`NOTICE`](NOTICE), and the component license files
for attribution and source revisions. MAME was used as a behavioral reference
during development.
