# RE Frontier — the ordered RE dependency chain toward a faithful port

Tracked by `tools/re_frontier.py` (consult it FIRST; update it in the SAME commit
that changes a step). This is the fine-grained companion to `docs/codemap.md`:
the codemap says *what subsystem exists*, this says *which ordered RE step is
real reverse-engineering vs a hack that jumped ahead*.

**Hard rule (no hacks / no fallbacks):** a `⛔ hack` status is DEBT, never an
acceptable resting state. It marks a shortcut standing in for absent RE and MUST
be removed as its real mechanism lands. `re_frontier.py hacks` is the debt list;
`re_frontier.py next` tells you the next RE-ready step.

**`re-verified` MEANS FAITHFUL to the real target — not "the mechanism runs."** A
step is `re-verified` only when its OUTPUT matches the real game/binary (look /
sound / behavior) on real data. An internal trace ("bytecode reached the call
site", "N rows attached") is a mechanism check, NOT faithfulness — if it runs but
the result doesn't match the real target, it is `re-partial` with the
faithfulness gap named. The user observes the running system; that observation
overrides any internal trace.

**Fail fast & loud:** a failure must surface loudly, never silently fall back —
unless the fallback IS intended behavior of the real target being reproduced.

Statuses: ✅ re-verified · 🟡 re-partial (honest gap) · 🔬 in-progress ·
⛔ hack (debt, must remove) · ⬜ todo · ➖ skip-by-design · ✍ authored · ⏸ blocked (computed).

<!-- Entries are `## <area>` sections holding `### <id> — <title>` headings, each
     followed by `- <field>: <value>` lines. Prose ANYWHERE in this file — before,
     between or inside entries — is yours: tools/re_frontier.py edits only the
     field lines it is told to change and copies everything else through byte for
     byte, refusing the write if anything else would be lost. -->

## build

### build.byte-exact — The disassembly assembles to the NTSC ROM byte for byte
- status: re-verified
- deps: 
- evidence: asar 1.81 on src/main.asm with data ripped by tools/rip_assets.py from the NTSC ROM (CRC32 D63ED5F8) writes an image equal to it (`cmp`), with schmidt's labels added
- where: src/, tools/rip_assets.py
- gap: 
- notes: schmidt's tools/decomp_symbols.py runs the build and the comparison

### build.symbols — Named labels exported as the symbol map
- status: re-verified
- deps: build.byte-exact
- evidence: symbols/sm_ntsc.sym is the [labels] section of asar's WLA symbols from the byte-exact build, anonymous labels dropped
- where: symbols/sm_ntsc.sym
- gap: 
- notes: low RAM is filed under bank $00; consumers map it to $7E

## engine

### engine.port-sites — Instructions the schmidt port hooks carry local labels
- status: re-verified
- deps: build.symbols
- evidence: each label sits on the instruction schmidt's override replaced by address before ($80:AE75, $84:8759, $86:81DD, $82:DFAC, $82:DFBD, $82:E1DC, $A0:8A4F, and the four ADC RoomMapX sites); the build stays byte-exact
- where: src/bank_80.asm, src/bank_82.asm, src/bank_84.asm, src/bank_86.asm, src/bank_90.asm, src/bank_A0.asm
- gap: 
- notes: 

### engine.ram-formats — Formats of the RAM schmidt reads
- status: re-verified
- deps: build.symbols
- evidence: Layer2ScrollX bit 0, ReserveTankMode values, Samus state handlers, TileTable, Scrolls, LevelData and SRAMMirror_LoadingGameState notes in src/memory.asm, each relied on by a schmidt mod shown working in the product
- where: src/memory.asm
- gap: 
- notes: 

## hud

### hud.split — The HUD/playfield IRQ split and the HUD tilemap
- status: re-partial
- deps: build.symbols
- evidence: InterruptCommandPointers ($80:9616): every begin-HUD command writes $2109=$5A, $212C=$04 at line 0 and returns V=$1F H=$98; a capture through gameplay, doors and the pause screen shows DP_IRQCmd 4/8/$16 with NMITIMEN $B1, and 0 with $81 before gameplay
- where: src/bank_80.asm InterruptCommandPointers; HandleHUDTilemap_PausedAndRunning ($80:9B44)
- gap: the HUD tilemap's contents (HandleHUDTilemap_PausedAndRunning) are read but not yet checked against a host-drawn HUD
- notes: 
