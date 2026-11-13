# Prior art

Sources for Super Metroid (NTSC) and what the schmidt port takes from each.


| Project | What it is | Licence | What we take |
|---|---|---|---|
| [mstan/SuperMetroidRecomp](https://github.com/mstan/SuperMetroidRecomp) | Super Metroid on mstan's snesrecomp framework, with widescreen (Fit, 16:9, 21:9, 32:9), an anchored HUD and mods | PolyForm Noncommercial 1.0.0 | design only (plan 0001); no code |
| [snesrev/sm](https://github.com/snesrev/sm) | C reimplementation, "early version", dormant since 2023 | none stated | evidence only |
| [RadzPrower/Super-Metroid-Launcher](https://github.com/RadzPrower/Super-Metroid-Launcher) | launcher for snesrev/sm | MIT | nothing yet |
| [vg-json-data/sm-json-data](https://github.com/vg-json-data/sm-json-data) | room, door and logic data for Super Metroid randomizers | CC BY 4.0 | room names by room header (`src/game/room_names.cpp`, revision 0ace1edd0933); its 261 `roomAddress` values equal the rooms our door-graph walk finds in this ROM |
| [InsaneFirebat/sm_disassembly](https://github.com/InsaneFirebat/sm_disassembly) | relocatable asar disassembly built from P.JBoy's bank logs; its test build must equal the NTSC (Japan/USA) ROM byte for byte, and asar writes a WLA symbol file | 0BSD | this repository is our fork of it, pinned in schmidt as `decomp/`; our additions are labels on the sites schmidt hooks, RAM format notes, `symbols/` and `docs/` |
| [metroidret/sm](https://github.com/metroidret/sm) | an older copy of InsaneFirebat/sm_disassembly (last commit 2026-01-03) | 0BSD | superseded by its upstream |
| [PJBoy/SM-SPC](https://github.com/PJBoy/SM-SPC) | assemblable source of the game's SPC audio engine | ISC | nothing yet |
| [JimLundin/sm-decomp](https://github.com/JimLundin/sm-decomp) | clean-room Rust model of the game's behaviours, checked against the ROM; early | MIT | nothing yet |
| [MrJackSpade/SuperMetroidDecomp](https://github.com/MrJackSpade/SuperMetroidDecomp) | C# reimplementation | none stated | evidence only |
| [strager/supermetroid](https://github.com/strager/supermetroid) | disassembly | none stated | evidence only |

## Building it

`tools/rip_assets.py` (upstream, stdlib only) reads the ROM given as its argument, checks its size,
CRC32 and SHA-256, and writes only the slices of it under the directory given by `-o`; it runs
nothing and opens no network connection. `tools/ff_file.py` writes a 3 MB file of $FF bytes for
asar to assemble over. asar 1.81 is built from RPGHacker/asar tag v1.81 (commit a8538ca8); the
bundled `asar.exe` and `asar-standalone` are not run. schmidt's `tools/decomp_symbols.py` does all
of this outside the tree, compares the image with the ROM and writes `symbols/sm_ntsc.sym`.
