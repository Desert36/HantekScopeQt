# Changelog — HantekScopeQt (desktop)

All notable desktop changes: new functions, hardware support, format support and
bug fixes. Newest first.

- **Scope:** Windows desktop application only. The Android app shares version
  numbers for most releases, but its history lives in the Android tree.
- **Dates:** local release dates. Where a version was rebuilt without a version
  bump, the rebuild is folded into that version.
- **1.6.2** was an Android-only release; there was no desktop 1.6.2.
- **1.6.23** was superseded before release by **1.6.23.1**.
- Builds before **1.6.0** are the pre-release development line and are not
  itemised here.

### Release index

| Version | Date | Headline |
| --- | --- | --- |
| Unreleased | 2026-11-24 | ATS eSCOPE ELITE `.tdms` import/export; Misfire / Relative Compression |
| 1.6.26.2 | 2026-11-23 | All 12 serial protocols enabled; decoder UI translated |
| 1.6.26.1 | 2026-11-23 | Crank/Cam roles follow the channels; simulator covers 1–12 pulses |
| 1.6.26 | 2026-11-22 | Crank/Cam page, sensor simulator, complete pt/es catalogs |
| 1.6.25.4 | 2026-11-22 | Vendor-format round: sample-and-hold, thread crashes, `.xls` import |
| 1.6.25.3 | 2026-11-21 | 1.00 px traces, Pico-grade palette and buttons |
| 1.6.25.2 | 2026-11-21 | Sub-pixel trace geometry, continuous envelope, 8× MSAA |
| 1.6.25.1 | 2026-11-21 | CPU trace-pen fast path (41 → 3 ms) |
| 1.6.25 | 2026-11-20 | Multimeter, device test, FX2 firmware, wide hardware support |
| 1.6.24 | 2026-11-19 | HScope `.zip`, Autel `.MSData`, device discovery, 6022 |
| 1.6.23.1 | 2026-11-14 | Full playback ladder, translations, 21 MB installer |
| 1.6.22 | 2026-11-12 | GPU trace backend, Average 8, Autel clamp ranges |
| 1.6.21 | 2026-11-10 | Autel clamp by name, export helper, demo unlock |
| 1.6.20 | 2026-11-07 | Visible/decimated ghosts, smooth joins, ghost domain fix |
| 1.6.19 | 2026-11-04 | Autel MaxiScope support, current-clamp model, `offsetValue` |
| 1.6.18 | 2026-11-02 | Per-probe range ladders, channel state, zoom mini-grid |
| 1.6.17 | 2026-10-31 | Saved view state, CSV/MAT parity, stream log |
| 1.6.16 | 2026-10-31 | Custom probes, probe-aware I/O |
| 1.6.15 | 2026-10-30 | Stale-trace fix, compact gutters, Pico axis |
| 1.6.14 | 2026-10-29 | Serial decoding (30 engines), `.hantek` v3 compression |
| 1.6.13 | 2026-10-29 | Pico-compact chrome, vehicle details |
| 1.6.12 | 2026-10-28 | Native `.psdata` import |
| 1.6.11 | 2026-10-28 | Degree rulers |
| 1.6.10 | 2026-10-26 | Touch pan and pinch zoom |
| 1.6.9 | 2026-10-24 | Block/roll timebase calibration |
| 1.6.8 | 2026-10-23 | Demo/licence UX, ruler latch |
| 1.6.7 | 2026-10-21 | Brand purge, rail state |
| 1.6.6 | 2026-10-19 | Trigger on the drawn trace |
| 1.6.5 | 2026-10-19 | 50 ms/div block root cause |
| 1.6.4 | 2026-10-19 | Demo unlock, perpetual licence |
| 1.6.3 | 2026-10-18 | `.hantek` v2, 50 ms/div fix |
| 1.6.1 | 2026-10-16 | Demux rule 4, per-channel V/div |
| 1.6.0 | 2026-10-15 | First installer of the current line |

---

## [Unreleased] — working tree, 2026-11-24

Automotive Test Solutions eSCOPE ELITE (`.tdms`) waveform support. Not yet
released; verified against the ATS demo library and against the installed
`eSCOPEELITE.exe`, which opens and re-saves the files this build writes.

### Added

- **Native `.tdms` import and export** (`src/TdmsFile.{h,cpp}`), implementing
  the NI TDMS 2.0 container exactly as ATS writes it:
  - one float32 volts block per channel, object paths `/'Data'/'CH1:'` …
    `/'Data'/'CH8:'`;
  - properties `wf_start_time` (ATS order: fraction, then seconds since
    1904-01-01 UTC), `wf_start_offset`, `wf_increment`, `wf_samples`;
  - ATS's segment lead-in, relative offsets, one segment per channel and the
    eight-slot metadata table.
- **`.tdms` everywhere a capture format is offered:** Open dialog, Waveform
  Library list and detail pane ("eSCOPE ELITE capture"), ghost/reference
  overlay, and the Save format list
  (`Automotive Test Solutions eSCOPE (*.tdms)`, type 10).
- **Content sniffing:** a TDMS capture renamed to `.csv` or any other accepted
  extension is still recognised by its `TDSm` lead-in.
- **Vehicle details carried across:** the ATS year/make/model/engine/
  transmission/description block is staged like a Pico capture's metadata, so a
  later Save to any other format keeps the identity.
- **Channel placement by name:** `CH5` lands on lane 5 even when `CH1`–`CH4`
  are empty, matching the tool that wrote the file.
- **pt_PT and es catalog entries** for the new visible format string; protocol
  and product names stay English.
- **`tdms_probe` developer tool** (dump, directory scan, byte-shape round trip);
  not shipped with the installer.

### Added

- **Misfire / Relative Compression page** (separate full-window overlay, next to
  Crank/Cam in the top bar):
  - **CKP mode:** crankshaft velocity from the CKP sensor; misfiring cylinder =
    slowdown during its power stroke (ATS CKP Misfire method). Firing order,
    trigger cylinder and waste-spark toggle.
  - **No-CKP mode** (tick box): coil primary ramp analysis (rounded ramp =
    GOOD coil, flat/square = BAD coil) plus injector pulse detection, per
    channel mapped to its cylinder (A=1 … H=8).
  - Per-cylinder verdict table (GOOD / MISS / SUSPECT with ratio vs median),
    RPM readout, and a trace strip of the analysed channel.
  - Works on live captures, imported waveforms (`.tdms`, `.psdata`, `.hantek`,
    `.csv`, `.mat`) and the sensor simulator.

### Notes

- Samples are written as **input volts**, the domain every reader returns, so
  save → re-open → load in ATS shows the same trace.
- ATS's own re-save is lossy: it rewrote a 25 kS/s 8-channel export as
  12.5 kS/s and altered CH2–CH4 extremes. This is an ATS behaviour, recorded
  rather than "fixed".
- Oddly-spaced CKP wheels without a clear missing tooth (e.g. some Honda
  engines) cannot anchor absolute angle — a known ATS limitation, recorded
  rather than "fixed".

---

## [1.6.26.2] — 2026-11-23

The serial-decoding toolbox reaches full protocol coverage: all 12 former "(not available)" tiles now decode, every config page opens, and the decoder UI is translated while protocol names stay English.

### Added

- **Every serial-decoding protocol is selectable.** The 12 tiles that read
  "(not available)" — CAN XL, FlexRay, SENT SPC, SENT Slow, IsoSPI,
  BroadR-Reach, 10BASE-T1S, I3C, Ethernet (10BASE-T/100BASE-TX), DCC and USB —
  are now real decoders with fields and columns, dispatched from
  `runDecoder()`.
- **Decoder UI localisation:** categories, field labels, enum options, source
  slots and column headers translate through `i18n::trData()`
  (`localiseRegistry()`); protocol names stay English, like units.
- **`tools/i18n_decoders.py`** (plus pt/es data) pulls registry strings into the
  catalogs, because `lupdate` cannot see `trData()`.

### Fixed

- **Next opened CAN for every protocol:** the tile handler never called
  `pickProto()`, so `m_protoId` stayed `"can"`. Next now opens the selected
  protocol's configuration page.
- **Decoder edge cases found by the synthetic encoders:** DCC separator/end
  bit, the Manchester/DME mid-cell edge grid, MLT-3 symbol grid and 4B/5B
  alignment, and Ethernet II payload length (validated by the FCS so trailing
  idle bits no longer shift it).
- **Missing decoder-table translations:** `src/SerialDecode.cpp` joined the i18n
  scan list, which is what had left Binary/Decimal/Hex/ASCII-Hex untranslated.

### Notes

- PHY-level engines (CAN XL, FlexRay, IsoSPI, BroadR-Reach, 100BASE-TX) are
  marked **experimental**: synthetic round-trip only, with a grey note on the
  config page.
- Self-test: `SDECODE-OK 31/31` (was 21/21), `ALL-GUIDED-PASS` in en/pt_PT/es,
  `I18N-OK 2 catalogs`; 1336 translated messages.

---

## [1.6.26.1] — 2026-11-23

A stabilisation release for the new Crank/Cam page: roles now follow the channels on screen, the simulator mirrors the measurement, and the installer gains a driver-free option.

### Fixed

- **Crank/Cam roles followed stored settings instead of the screen.** With an
  imported waveform the live hook never ran, so the measurement used the stored
  channel ("it only works if I change the crank to CH1"). The analysis now reads
  the source and roles from the page's own widgets, with QSettings only as a
  pre-page fallback, and is keyed to the channel index, never a label.
- **Role combos kept the previous file's channel names.** `importFiles()` now
  resets all eight labels and rebuilds the three role lists from
  `setChannelLabel()`, as does opening the page.
- **A page could open on roles that cannot be measured.** `showCrankCam()`
  rebuilds, measures, and only lets the scan choose (`m_ccAutoPick`) if that
  fails; "Calculate teeth" still never moves a role.
- **"No measurement yet" gave no reason.** An empty lane or a channel without a
  tooth pattern now says why in the Measured panel.
- **`analyseCam()` accepted a flat lane** and turned mean-crossing chatter into
  a fake pulse train (a flat CH4 read as "8 pulses").
- **"Calculate teeth" fills what it names:** the simulator crank pattern
  (60-2, 36-1, 36-2+, 136) and the manual Teeth/Missing boxes, leaving "Use
  manual count" alone.
- **Simulator cam patterns now cover every measurable count:** single pulse
  (1), 2–12 pulses/cam turn, and no cam signal. `SensorSpec` gained
  `cmpSlots`/`cmpExSlots`, so the live simulated capture follows the selection;
  the report names the item text, not the stored id.

### Changed

- **Installer:** the Drivers group is checkable (driver-free install is a
  visible choice); Autel MP408 is off by default; selecting the Hantek 1008C
  driver ticks "Remove old Hantek drivers", which is also default-checked so
  the page matches the behaviour; `installer/qtifw/packages/*/meta` is now
  versioned.
- Catalogs: 1196 messages, 0 unfinished, both languages.

---

## [1.6.26] — 2026-11-22

Adds the Crank/Cam correlation toolbox with a sensor simulator, completes the Portuguese and Spanish catalogs, and fixes the role and analysis problems found in first testing.

### Added

- **Crank/Cam correlation page:** pick any channel for the crank and either cam,
  a Measured readout, a strip view, degree rulers, and a **sensor simulator**
  with 60-2, 36-1, 36-2-2-2 and 136-tooth crank patterns.
- **Complete pt_PT and es catalogs** (1189 messages, 0 unfinished) with a
  reproducible generator (`tools/i18n_ts.py --write`) and the
  `fold-ts-contexts`/`normalize-ts-indent` helpers needed because
  `LUPDATE_OPTIONS -tr-function-alias` folds `TR()` strings into their class.

### Fixed

- **Channel roles were not remembered:** `saveCfg()` wrote through a cached
  QSettings and never flushed, so a killed build returned to CH1/CH2/CH3. The
  role combos now save and `sync()`.
- **Changing a role stalled the waveform:** live analysis cost ~45 ms per pass
  in the GUI thread. It is throttled to once a second, skipped when
  source/roles/crank length are unchanged, gated on the crank channel, and the
  cam window is capped at 500,000 samples.
- **Changing a role or source did nothing until the page was reopened:**
  `recalcNow()` is connected to every control that feeds the readout.
- **"Calculate teeth" rewrote the role selection;** it now leaves all combos
  alone and only reports what the scan prefers.
- **Any channel may fill any role:** the analysis is keyed to the channel index,
  so an exhaust cam in the inlet slot works for comparison.
- **The simulator pattern combos store a stable English id**, so the
  "no crank signal" choice survives a language change.

---

## [1.6.25.4] — 2026-11-22

Vendor-parity round against Hantek's own software: it removes the sample-and-hold staircase, three thread-shutdown crashes, the block/roll boundary error and the flat-line vendor CSV import, and adds the `.xls` interchange format.

### Added

- **Hantek `.xls` multi-channel import** (`#CHANNEL` / `#CLOCK` / `#SIZE` /
  `#UNITS`, one block per channel), detected by content — an `.xls`, `.rfc` or
  `.txt` holding that layout opens.
- **Hantek CSV import:** the vendor CSV carries `#CLOCK` (time per division, in
  seconds) and no time column; the importer now synthesises the axis instead of
  reading samples as time.
- **10,000 s and 20,000 s timebase divisions**, appended without shifting the
  existing ladder.
- **`.xls` and `.rfc` in the Open filter and Library list.**
- **Installer asks for admin rights at launch** (Introduction page), so the
  UAC prompt no longer appears on the install page after folder selection.

### Fixed

- **Sample-and-hold manufactured squares.** A step was decided by the size of
  one move (`|dy| > 6 px`), so any large-amplitude signal became a staircase.
  A step now requires two held levels. Sample-and-hold itself is opt-in
  (Settings → "Sample-and-hold on slow timebases", default **off**).
- **Stop froze, then closed the app.** Three sites did `stop(); wait(1500);
  deleteLater()` even when the wait timed out; a block shot cannot be
  interrupted, so a running QThread was destroyed and the device read returned
  into freed memory. The thread now deletes itself on `finished`; close/exit
  orphan it instead of taking it down.
- **The block/roll boundary was one step too far:** `roll0()` is now 17 —
  100 ms/div uses block shots (matching the vendor's 8-channel export) while
  200 ms/div stays live on roll. The shot timeout scales with the division and
  the first five shots after a re-arm no longer count as misses.

### Removed

- **The `.rfc` export.** Hantek's Load rejects our text under both `.rfc` and
  `.wms` (out of memory), so exporting a file their software cannot read was
  misleading.
- **The `HS_LANES` experiment:** arming only on-screen channels raised the rate
  but changed the waveform on a channel toggle, because the demux samples at
  different instants per lane count.

---

## [1.6.25.3] — 2026-11-21

Visual-parity release: the trace returns to a flat 1.00 px, the channel colours match PicoScope's documented palette, and the buttons adopt Pico-style styling.

### Changed

- **Trace thickness is a flat 1.00 px default** (one-time `defs1627` migration
  resets a stored 1.00/0.75/0.65). The 1.6.25.2 dpr-aware 0.75 default is gone:
  PicoScope offers 1 and 3, and 1 logical px is `dpr` device pixels at every
  scale.
- **Pico-grade channel palette:** A `#1E6FE8`, B `#EA2626`, C `#16BE43`,
  D `#D99A00` (plus E–H), from one table in `Pico.h` so legend, buttons, meter
  accent and badges move together.
- **Pico-style buttons and stylesheet.**

---

## [1.6.25.2] — 2026-11-21

Trace-quality release: sub-pixel vertices, a continuous min/max envelope and a coverage floor remove the last thin-trace dropouts; 8x MSAA becomes an option.

### Added

- **Sub-pixel trace geometry:** vertices keep their true fractional y instead of
  being truncated to a whole logical pixel.
- **Continuous envelope:** adjacent columns join where the step is shallow, so
  the band is continuous instead of a comb of butt-ended bars.
- **Sub-pixel coverage floor:** a stroke or bar under one device pixel is
  expanded onto a whole device row, so 4× MSAA cannot round a flat digital run
  out of the picture.
- **Opt-in 8× MSAA** (Settings, restart required; `HANTEK_MSAA` still
  overrides).

### Changed

- **0.65 px default**, then corrected to a **dpr-aware 0.75 px** default
  (0.75 below 150 % scaling, 1.00 at 150 %+ so the trace does not halve on a
  HiDPI display).
- Measured: envelope lattice concentration 0.595 → 0.242 (Cadillac) and
  0.504 → 0.198 (Santa Fe); flat-run drop-outs 12 → 6 runs per 800 columns;
  ink and width essentially unchanged.

---

## [1.6.25.1] — 2026-11-21

Performance release for the CPU renderer: a cosmetic 1 px pen removes the 41 ms paint cliff, taking a settled frame from about 20 to 60 fps without changing a pixel.

### Fixed

- **The 1.00 px CPU trace-paint cliff.** A non-cosmetic pen wider than one
  device pixel is stroked as a filled path, costing ~41 ms per frame at
  dpr 1.25. All seven trace pens now use one `tracePen()` helper with a cosmetic
  1.0 pen while `m_lineW <= 1.00` and `m_lineW * dpr <= 1.3`; a raised thickness
  keeps the geometric pen and its exact width.
- Measured, same capture: create-trace 41.08 → 3.20 ms; p50 50.22 → 16.67 ms
  (19.9 → 60.0 fps); drag p50 35.33 → 10.15 ms. The new 1.00 px frame is
  byte-identical to the old 0.80 px frame (0 differing pixels of 1,497,084).

### Changed

- **QtIFW installer keeps four-part versions** (`1.6.25.1` sorts above `1.6.25`
  and upgrades in place).

---

## [1.6.25] — 2026-11-20

The hardware-expansion release: multimeter page and jig, device test, FX2 firmware upload, nine new device families, and a Qt Installer Framework-only installer.

### Added

- **Multimeter page and wiring jig** (`MeterPanel`, `help/meter-jig.html`).
- **Device test:** Settings → "Test device…", `--device-test <id>`, log written
  to `device-test-<stamp>.txt`, email line for owners.
- **Wide hardware support:** Hantek 2090/2150/2250, FX2 clones, Hantek 6022,
  6000 series, Autel MP408, USB Autoscope, USBTMC (ScpiScope), Owon, plus device
  discovery.
- **FX2 boot firmware:** 11 images plus `FIRMWARE-NOTICE.txt`, installed to
  `<app>/fw` as the first search path; a shared raw/Intel-HEX loader
  (`src/Fx2Firmware.h`) fixes the 2090 silent-zero upload.
- **Help rebuilt:** new Test device, Supported devices and drivers, Multimeter
  and hardware detection, and File formats sections; 19 sections in en/pt/es.

### Changed

- **Installer is Qt Installer Framework only.** The Inno `.iss` scripts are
  removed; `prepare-qtifw` fails the build if the firmware or help/image payload
  is incomplete.

### Fixed

- **Blank help pages** caused by JavaScript syntax faults in the embedded HELP
  array.
- **`.MSData`, USB Autoscope `.mwf`, Hantek `.xls`** and the other vendor
  containers are recognised by content, not just by extension.

---

## [1.6.24] — 2026-11-19

File-format and discovery release: HScope `.zip` and Autel `.MSData` join import/export, the Library gains multi-select and per-save folders, and PicoScope/6022 hardware becomes discoverable at runtime.

### Added

- **HScope (`.zip`) import and export.** v4 keeps metadata in the ZIP comment
  with one 16-bit `dataN` entry per channel; v3 floats live in `data.hxml`.
  `src/HScopeFile.cpp` reads both and writes v4 with a stored/deflate ZIP layer
  and a preview PNG. Nothing from HScope is linked.
- **Autel MaxiScope single-file `.MSData` import and export** (`MSDataFile`),
  with a header sniff and a Save row "Autel MaxiScope single file (`*.MSData`)"
  (type 8). `.psdata` → app → `.MSData` → app → `.psdata` is byte-identical.
- **Library multi-select** (`ExtendedSelection`); "Open as buffer" imports every
  selected file through the same path as the Open dialog.
- **Per-save subfolder:** formats that write one file per waveform place the
  numbered set in `<folder>/<save name>/`, so two saves cannot be imported as
  one set.
- **Live-device groundwork:** `ScopeDevice.h` backend interface; PicoScope
  `.ps2000a`/`.ps3000a`/`.ps4000a`/`.ps5000a` resolved through `QLibrary` at
  runtime (nothing linked or bundled); `device_probe`; scope auto-detect with a
  drop-down fallback; Hantek 6022 SDK backend; device discovery.

### Fixed

- **A plain ZIP is not an HScope capture:** `.zip` is accepted only on a content
  sniff (metadata comment or `data.hxml` entry), never by extension, in both the
  Open dialog and the Library.

---

## [1.6.23.1] — 2026-11-14

Playback and packaging release: the full 500 ns...5000 s ladder works, the installer shrinks to 21 MB, and the pt_PT/es translation layer plus external help pages ship for the first time.

### Added

- **Full playback ladder:** 500 ns/div … 5000 s/div, every step accepted, with
  fractional windows below four samples (`playWinF`), a bracket sample in the
  slice, and the Scope lozenge reading the exact window instead of the integer
  slice. `dbgPlayDivWalk` asserts the lozenge at all 31 steps.
- **Translation layer** (`src/I18n`) with pt_PT and es `.ts` catalogs embedded
  through `lrelease`.
- **External help pages.**

### Changed

- **Trace thickness default 1.00 px**, with a one-time reset of the stored legacy
  1.25.
- **Installer reduced to 22,442,894 B:** no `probe_init.exe`, no Qt6Pdf/Svg/
  Network, only the platforms plugin.

---

## [1.6.22] — 2026-11-12

Graphics and Autel release: the GPU trace renderer becomes the default, traces snap to device pixels with an Average 8 filter, and Autel clamps decode by probe range.

### Added

- **GPU trace backend as default** (`GlTraceRenderer`): batched GL quads/dots,
  MSAA, clip/scissor, persistent per-slot VBO cache. The CPU renderer remains
  the literal previous paint body as a rollback (`HANTEK_PAINT=cpu` or
  Settings).
- **Average 8 display filter**, clamped to one screen column and edge-gated, so
  a flat window averages while a window across a step passes raw and a live
  square wave stays square.
- **Autel capture containment in the Library:** moving or deleting an Autel
  `.json` moves/deletes the whole capture container, so page folders and `.dat`
  payloads cannot be orphaned.
- **Library folder management:** New folder (accepts `Brand/Model` paths), Move
  to folder, Delete (Recycle Bin first), Save current here.

### Changed

- **Trace columns snap to whole device pixels** (fewer staircase tips; Suzuki
  tips 17.3 → 8.1 per 1,000, Cadillac 3.5 → 2.5).
- **Trace thickness default 1.00 px**; Settings gains a GPU on/off box.
- Installer refresh adds Qt6OpenGL/Qt6OpenGLWidgets and keeps `opengl32sw` for
  software GL; drops `probe_init.exe` and the separate demo installer.

### Fixed

- **Zero-length segments** now draw the round cap, and connectors are real
  segments instead of flattened to `x1` (the dotted flat run).
- **Autel current clamps are decoded by probe range:** the CC-650's 200 A lane
  uses 100 A/V, so a 573-code injector pulse reads 140.11 A as Autel does.
- **Autel export echoes each lane's `offsetValue`**, so MaxiScope reopens every
  trace at the same vertical position; our own reader still keeps its lane
  panning, so round-trip bytes are unchanged.

---

## [1.6.21] — 2026-11-10

Autel round-trip release: clamps are recognised by their real short names (fixing a 20x current error), the helper registers one capture at a time, demo opens all functions on channel A, and the Library gets folders.

### Added

- **Autel clamp resolved by name.** `hs::Autel::isCurrentChannel()` is the single
  recognition point (unit synonyms `A`/`Amps`/`ampere`/decorated/`mA`,
  `probeUnitFullName == ampere`, and every clamp spelling); `shortProbeName()`
  maps our labels to Autel's built-in short names; `canonicalUnit()` writes `A`
  and keeps `mA`.
- **Autel export helper:** `autel_register.py` and
  `Register in Autel library.bat` are written beside the `.json`. Run from
  inside a capture folder, the script registers only that capture; it still
  refuses while Autel is open, backs up `config.db`, prints the row and only
  ever INSERTs.
- **Demo mode unlocks all functions on channel A** — capture, review, save, open
  and print; B–H still need activation.
- **Waveform Library folders:** New folder, Move to folder, Delete, Save current
  here, folder rows with Up + breadcrumb, and a detail pane with real channel
  labels and tick boxes.

### Fixed

- **Autel current lanes decoded 20× low** (a 10 A sine read 0.5 A). The writer
  now emits `CC-65[20A] / A / ampere / 100`, and import/export share the same
  recognition.
- **Round-trip drift:** save-model echo (`continuumModel`, `sampleTime`,
  `sweepFillRatio`), the file's own `probeUnitFullName` spelling, and the
  active page (`curPageNum`) are preserved; `imgUrl` is informational, not a
  content difference.
- Ten-case round-trip matrix (16-page CC-650, 9-page CC-65, partial sweep, our
  own exports, synthetic fixture) exits 0 with zero JSON/`.dat` differences.

---

## [1.6.20] — 2026-11-07

Ghost/reference release: overlays are lifted, decimated and probe-corrected, and short trace joins are smoothed only while the view is still.

### Added

- **Visible ghost/reference overlays:** a fresh ghost is lifted 0.25 grid
  divisions so an overlay of the same recording is visible instead of sitting
  under the live trace; a double-click still drops it to `dy = 0`.
- **Ghost decimation:** one min/max stroke per pixel column, O(pixels) however
  long the file is.
- **"Smooth trace joins":** strokes shorter than 40 px are antialiased in one
  pass, and only when the frame has settled (150 ms after the last pan/zoom/
  data change). Persisted as QSettings `smooth`, default on.

### Fixed

- **A channel-C pressure ghost was drawn in volts** (59× too small). Overlay
  samples that are input volts now pass through `probeDisp(lane, v)` before
  `toPlot()`. After the fix `r = 1.010`; the 1 % residual is the probe's input
  filter, deliberately not applied to a ghost.
- **The thickness cliff was measured as pen width, not the AA hint** (0.8 → 0.9
  px costs 2.5–3×). No default was changed in this round.

---

## [1.6.19] — 2026-11-04

Autel MaxiScope release: native import/export of Autel's `.json` + `.dat` container, a correct current-clamp model, and `offsetValue` treated as a lane displacement.

### Added

- **Autel MaxiScope support.** The container is read and written natively:
  `<root>/ScopeData/<yyyy-MM-dd-HH-mm-ss>/<name>.json`, page folders with
  little-endian int16 `.dat` per channel, and `ScopeImg/<name>.png`. A genuine
  Autel original round-trips field for field.
- **Current-clamp lane decode:** Autel's current decode is
  `code * fullScale² / 4096`, so a clamp lane sits 20× above its voltage axis;
  the amplitude model now matches Autel's own screen.
- **`offsetValue` is a lane displacement, not the axis centre**, applied on
  import and export.
- **Library detail for Autel captures**, with page folders hidden from the file
  list.
- **Paint-speed work on the CPU renderer** (the pre-GPU path).

### Fixed

- **A stacked Autel lane read as −32…8 V** when `offsetValue` was treated as an
  axis midpoint.
- **The first 1.6.19 installer was superseded** by the 02:41 rebuild that
  carries the corrected `offsetValue` model.

---

## [1.6.18] — 2026-11-02

Probe-ladder release: per-probe V/div ladders step with +/- and survive both formats, channel on/off state persists, and the Zoom box becomes a miniature of the grid.

### Added

- **Per-probe range ladders:** harvested from Pico's own probe definitions,
  parsed and stepped. `+`/`-`, the mouse wheel and the V/div combo walk the
  rungs; import marks the rung the file was saved on. The ladder travels in
  both `.psdata` and `.hantek`; no Pico probe definition is bundled.
- **Channels panel opens wide enough for all nine columns** (890 px).

### Changed

- **A channel switched off stays off in both formats**, keeps its lane, and
  keeps its rulers saved. A greyed Rulers row can be switched on again.
- **Zoom box redrawn as a miniature of the grid:** shared vertical space, lanes
  overlap exactly as they do on screen.
- **A re-save keeps the capture's own timestamp** instead of the save time.
- **Save honours Current/All**, and Pico CSV + MAT carry the channel range.

### Fixed

- **Pico's two-`<channelconfig>` rule:** the crash and the rail "Off" state;
  built-in x1 probe identity reuse.
- **Over-range banner on a probe axis shifted off zero** treated a span as one
  rail (the Suzuki banner is gone).
- **Time-ruler fraction now uses the record**, not the decoded payload.
- **A `.psdata` sample touching a rail was read as over-range.**
- **The 8-channel rule** and `.psdata` staying at 4 channels.
- **A file's own unit label survives the `.hantek` round trip.**
- **A saved view used the wrong denominator:** Full reopened as a phantom zoom.

---

## [1.6.17] — 2026-10-31

View-state release: a capture reopens on the screen it was saved from, CSV/MAT imports reopen the saved screen, and the stream log is introduced.

### Added

- **Saved view state:** a capture reopens on the screen it was saved from.
  `.psdata` carries the open capture, graph zoom/pan, draw order, time/angle/
  volt rulers and phase preset in Pico's own elements; `.hantek` v3 gains a
  compressed `view` block. Split sets stage the view from the part that held the
  on-screen capture.
- **CSV/MAT import parity:** the writer and reader share one vocabulary, and an
  import reopens the saved screen instead of fitting each lane.
- **Stream log** (Save → *Stream...*).
- **Phase-scale presets** 360/720/100 %/custom; exact off-ladder V/div is kept
  verbatim.

### Fixed

- **The active phase preset was not marked** in the Rulers panel, and Pico's
  rotation base was not used as the preset boundary.
- **Badge range vs trace drag:** volt-ruler badges are hit-tested before channel
  columns, so a badge drag moves the ruler, not the channel.
- **Pico pan-y sign and record-based viewport fractions.**
- **The voltage axis now follows Y zoom**; time rulers insert into the zoomed
  view; voltage rulers read in their own channel's units.
- **Stable range badge.**

---

## [1.6.16] — 2026-10-31

Custom-probe release: a Pico-style three-step probe wizard and library, automatic probe registration on import, and probe-aware `.psdata`/`.hantek` I/O.

### Added

- **Pico-style custom probes:** a three-step wizard (Details, Scaling, Options)
  with Add/Edit/Delete library management. Scaling supports an equation
  `display = (f * volts + c) / unitMult(unit)` or a volts → display-units
  calibration table; Options cover coupling, 20 kHz bandwidth, auto range and a
  software low-pass with cutoff.
- **Imported captures register and select the probe stored in the file**, and
  that probe survives Save as `.psdata` and `.hantek`.
- **Two-level probe picker.**

### Fixed

- **Range badge printed values like `±2.6e+03 inHg`** because the probe was
  applied twice to an already display-domain V/div.
- **Over-range centre marker.**
- **Probe-aware `.psdata`/`.hantek` I/O.**

---

## [1.6.15] — 2026-10-30

Display-polish release: fixes the stale second capture, compacts the axis gutters, restores a 1.00 px trace, turns the display filter off by default, and adopts Pico's 1-2-5 time axis.

### Fixed

- **Stale trace when a second capture was opened:** two captures of equal length
  with equal display settings produced the same `PlayKey`, so the view kept the
  previous file's trace. `activateBuffer()` clears `m_playKeyValid`; the plot is
  now 0 px different between a fresh and a sequenced load.
- **Volt-ruler badge grab moved the channel** instead of the ruler, and the left
  badges were clipped to the gutter width.
- **A stale grid after a timebase change.**

### Changed

- **Compact axis gutters:** per-column width 44 → 34 px, base margins 2/2 px,
  channels alternate left/right in connection order (A/B/C/D left-right-left-
  right), decimals fit the column; 8 channels at 40 V/div show `200`…`−200`
  with no collisions.
- **Trace weight 1.25 → 1.00 px**, display filter **Off** by default, and a Pico
  1-2-5 time axis on round absolute times.

---

## [1.6.14] — 2026-10-29

Serial-decoding release: 30 protocol engines, a three-step wizard, in-graph tracks, result tables with search/filter/Data-to-text, and `.hantek` v3 compression.

### Added

- **Serial decoding framework** (`SerialDecode.h/.cpp`): 30 engines — CAN,
  CAN FD, J1939, NMEA 2000, LIN, RS232, SPI SDIO, SPI MISO/MOSI, I2C, SMBus,
  PMBus, SBS, I2S, SENT Fast, 1-Wire, PS/2, quadrature, Manchester,
  differential Manchester, DMX512, NMEA 0183, MODBUS RTU/ASCII, ARINC 429,
  MIL-STD-1553, parallel, DALI and PSI5. Twelve more were listed as
  unavailable, each with a note on what capture/timing model was missing.
- **Three-step wizard** (protocol tile → sources/threshold/hysteresis/invert and
  protocol fields → graph/table format, buffers, rulers), reachable again from
  an existing decoder row.
- **In-graph decoder tracks** under their source channel, with drag-to-stack for
  two decoders on one channel, hover summary and click-to-select packet.
- **Results table** per decoder: binary/decimal/hex/ASCII-hex, search, filters,
  CSV export, field editor, Data to text.
- **Search paints the whole row** Pico blue, arrows walk every match, boolean
  columns switch to a True/False pair.
- **Data to text:** "Create template" writes the header/description CSV through
  the normal naming/folder path; "Open" reads it back so the table shows
  translated text while search/filter still use raw values.
- **Baud keypad** popup with display, digits, MBd/kBd/Bd and presets.
- **`.hantek` v3 zlib compression:** separate compressed JSON and float32
  streams, atomic write preserved, v1/v2 still readable. Measured
  16,001,906 → 3,451,792 B (4.64×) on the Subaru crank capture.

### Changed

- Results strip capped at two-thirds of its size hint (262 → 198 px), so traces
  dominate.

---

## [1.6.13] — 2026-10-29

Chrome release: Pico-compact top bar and side rail, a Vehicle-details button, and correct Notes/Channel-labels tab behaviour.

### Added

- **Vehicle details button** at the end of the top bar, opening the same
  `VehForm` the Save dialog uses and staging it into `m_saveVeh` for every
  writer.
- **Notes / Channel labels strip** pages are inserted explicitly, so clicking
  Notes no longer drags Channel labels in and vice versa.
- **Label picker keeps its choice** (Pico behaviour) instead of resetting to
  "[Select label]".

### Changed

- **Pico-compact chrome:** top strip 54 → 48 px; three lozenges 118/138/110 ×
  42 px; icon buttons 58 × 42 with a uniform 20 px glyph box and 9 px captions;
  side rail 64 × 56 buttons, 22 px icons, fixed 156 px column; rail-to-grid gap
  cut (axis `baseL` 12 → 4).

---

## [1.6.12] — 2026-10-28

Format release: `.psdata` files are decoded in-process, removing the dependency on an installed PicoScope.

### Added

- **Native `.psdata` import, in-process** — no PicoScope installation and no
  `BatchConvert.exe`. The decoder is portable C++ with zlib only; it reads the
  container layout, the settings cipher and the per-channel sample scaling.
  Verified sample-exact against PicoScope 7's own CSV/MAT exports (3 buffers ×
  4 channels at 50 kS/s).

### Notes

- Installer compiled; the build was not deployed on desktop (Android shipped
  1.6.12 / vc22).

---

## [1.6.11] — 2026-10-28

Ruler release: PicoScope 6-style degree rulers with two independent endpoints, 0-720 degree markers, RPM/period and ruler-based calibration.

### Added

- **PicoScope 6 degree rulers:** the angle scale is defined by two independent
  endpoints (0° and 720° in samples), not by the grid. Dragging either endpoint
  stretches/shrinks the span, even past the visible window, and the intermediate
  markers redistribute at ¼, ½ and ¾.
- Vertical dashed lines at 0/180/360/540/720, boxed labels in a dedicated row,
  white square handles and degree chips; angle cursors.
- **Period/RPM spin** (`period = (end − anchor) / rate / 2`), "Rulers cover"
  360°/720°, and calibration from the two time rulers.

### Fixed

- **Off-view endpoints and cursors clamp to the plot edge**, with the 0° and
  720° handles separated when they share an edge; off-view time pills clamp and
  stack instead of disappearing.
- **Ruler pointers:** turning a channel's rulers off now also hides its value
  chips and stops hit-testing them.

---

## [1.6.10] — 2026-10-26

Touch release: fractional touch panning that can enter history again, plus two-finger pinch zoom of the time axis.

### Fixed

- **Touch pan now scrolls history both ways.** `onXPan()` keeps a fractional
  accumulator instead of casting each sub-sample delta to an integer, so every
  touch step no longer rounds to 0 and re-bases at the live edge.
  `xPanEnded()` clears the accumulator on release.
- **Two-finger pinch zooms time.** One finger is forwarded to the mouse
  handlers (double-tap re-detected); two fingers zoom the X span about the pinch
  centre. `WA_AcceptTouchEvents` is set.

---

## [1.6.9] — 2026-10-24

Timebase-calibration release: every block division is measured against a 1 kHz source, roll frames always carry eight lanes, and the roll grid fills from the left.

### Fixed

- **Block timebase calibrated against a 1 kHz generator:** 50/100 µs =
  500 kSa/s, 200/500 µs = 100 kSa/s, 1 ms = 50 kSa/s, 2 ms = 25 kSa/s,
  5 ms = 9375 Sa/s, 10 ms = 4688, 20 ms = 2344, 50 ms = 938; the fast block
  band 500 ns…100 µs runs at a fixed 500 kSa/s.
- **The ~500-sample block shot is cropped, never squeezed:** `blockWindow()`
  returns `10 × div × eff` samples and `sliceWin()` feeds exactly that crop to
  the view.
- **Roll frames always carry all eight lanes**, like block shots, so adding or
  removing a channel can no longer stretch the trace horizontally.
- **Roll timebases follow the grid** (stock Scan Mode): `pickRate()` picks the
  device rate that puts about one sample per pixel at every roll division, and
  the history is rescaled so the same time span stays on ten divisions.
- **Roll draws sample-and-hold**, so a square wave reads square (edge slant
  7 → 1 px, corner gap 8 → 2 px, plateau width ratio 0.955 → 0.995).
- **Scan fills from the left, never stretches:** a short record keeps its true
  `1/rate` spacing and grows rightward over blank grid; changing the roll div
  starts from an empty screen.
- **Time-axis labels equal the selected timebase division**, in ns/µs/ms/s,
  and the Scope lozenge shows the actual window/rate.
- **The 1.6.9 line keeps demux rule 4 unchanged**; the block path never calls
  `demuxStride()`/`demuxFactor()`.

---

## [1.6.8] — 2026-10-23

Demo/licence UX release: a permanent DEMO/ACTIVATED pill, dismissible demo intro, visible 25 V clamp, and a corrected `save=0` licence gate.

### Added

- **Permanent DEMO/ACTIVATED pill** in the status bar, visible in fullscreen;
  clicking opens Activation in demo and explains "no action needed" when
  activated.
- **"Don't show this again"** on the demo intro (QSettings `demoIntro=once`).
- **Visible 25 V clamp message** (once per session) instead of silently jumping
  to 5 V/div; limited paid keys get their own wording.
- **Cosmetics stay usable in demo:** theme, trace thickness, phosphor
  persistence and sample dots are display-only preferences.
- **`save=0` licensed keys** now allow the core controls (timebase, sampling,
  trigger) while still blocking save/print.

### Fixed

- **Rulers rail latch:** a checkable QToolButton re-toggled on mouse release
  after a long-press had already turned rulers off. The suppressed trailing
  click now calls `syncRailChecks()`.
- **Measuring-system selection persists** and is explained: US = psi/°F,
  Metric = bar/°C, readouts only; voltage, current, g and time keep their units.
- **Licence at rest:** a tampered stored key and a tampered stored HWID are both
  rejected by the desktop's `dsohal.dll` split.

---

## [1.6.7] — 2026-10-21

Chrome-consistency release: user-visible Pico branding is removed, rail buttons become checkable and state-driven, Views becomes non-modal, and the Rulers button becomes three-state.

### Changed

- **Brand purge of user-visible text:** 78 string literals rewritten
  ("Pico"/"PicoScope" → "the scope"/removed, "WPS500X" → "Pressure
  transducer", "FirstLook" → "Low-pressure transducer", plus format and
  self-test labels). Internal source identifiers were left unchanged.
- **Rail buttons are checkable and mirror the UI state** (Views, Measure,
  Rulers, Math, Trigger, Channels, Settings, Zoom); `syncRailChecks()` reads
  real visibility, so checked means the panel/window is open.
- **Views is non-modal**, and a second rail press closes it.
- **Rulers rail button is three-state:** tap when off = rulers on + panel; tap
  when on + panel up = panel hidden with rulers untouched; tap when on + panel
  down = panel shown; hold ~600 ms = rulers off and fresh next time.

### Fixed

- **The Rulers button sometimes stayed highlighted** after a long-press.

---

## [1.6.6] — 2026-10-19

Trigger-correctness release: the trigger fires on the drawn trace, is clamped to the visible range with a 1 % acceptance band, and anchors playback and readout to the firing point.

### Fixed

- **Trigger level is reachable and precise:** it clamps to the visible view
  range as well as ±5 divisions, binds to the source channel's unit, steps by
  V/div ÷ 10, and shows 2–6 decimals.
- **The trigger fires on the drawn trace, not the raw stream.** The search now
  applies `eff × gain`, probe zero, inversion and (for AC) the mean, then
  compares in display units. This fixes "fires at 1.83 V but not 1.90 V".
- **Edge acceptance band = 1 % of the screen span**, below the level for a
  rising slope and above for falling, so a slightly-off level still fires.
- **Stored samples / playback anchor to the trigger:** the record is searched
  with the same rules, the window anchors at `hit − pre %`, playback stops when
  the crossing enters the frame, and changing mode/source/level/slope/pre
  re-finds.
- **Latent crash in the Normal/Single anchoring loop:** it indexed
  `m_stream[c]` out of bounds when a channel was shorter than the trigger
  source; the window is now clamped to the channel's real extent.
- **Readout and badge show the value at the trigger point** (Normal-hold,
  Single, anchored stored sample); otherwise the newest sample.
- **Single mode marks the frame held**, so the chip/badge agrees with the
  lozenge.
- **"Activate…" greys out once activated.**

### Added

- **"Developed by Auto-Dec"** label in Settings.

---

## [1.6.5] — 2026-10-19

50 ms/div release: the trigger channel is sent to the device, its level is re-aimed at the signal, and a short or slow shot falls back to streaming instead of freezing.

### Fixed

- **The 50 ms/div block symptom.** The 1008C triggers on one channel and the app
  never sent it: `init1`/`init3` hardcoded channel A with the level at
  mid-scale. With A off or unconnected, every block shot waited out the trigger
  poll (~2.5–3 s per screen).
  - `setTrigSrc()` is applied when arming block shots and roll; the app follows
    an active channel when the selected source is switched off.
  - After each shot, the trigger level is re-aimed at the trigger channel's
    raw midpoint when it drifts by > 8 counts (span > 24 counts).
  - A short (< 400 samples) or slow (> 1.5 s) shot counts as a miss; after
    3 misses the worker streams roll at the block clock instead of freezing.
- **Licence fingerprint memoised** (one-time `computeHardwareId()`), value
  unchanged for existing keys.

### Changed

- **Timebase panel buttons carry their unit** (`500 ns`, `20 µs`, `50 ms`, …),
  so the same number no longer looks duplicated across sections.

---

## [1.6.4] — 2026-10-19

Demo/licence release: demo opens timebase, trigger and probe/coupling, licences become perpetual, and repaint and packaging defects are fixed.

### Changed

- **Demo unlocked** for timebase (div ladder + Sampling), every trigger control,
  and channel Probe + Coupling. Demo remains limited to channel A, 25 V, and no
  saving/printing/setup files/Auto setup/hardware zero/guided presets/offsets/
  gain/polarity/V-div ±/Settings.
- **Licences are perpetual:** the payload's fourth field is no longer a date
  and expiry checks were removed. Key format is unchanged, so issued keys still
  verify; new keys mint `99991231`.

### Fixed

- **Repaint guards:** `setWindow`/`setScale`/`setTrig`/`setRef`/`setMath`/
  `setCursors` early-return when nothing changed, so the live view stops
  rebuilding ~18×/s while the waveform changes ~1×/s.
- **The cached grid is allocated in device pixels** with
  `setDevicePixelRatio`, so scaled displays no longer upscale it per frame.
- **Packaging:** stage, stage-demo and the deploy folder now hold exe +
  `dsohal.dll` + `probe_init.exe` from the same build (the previous mismatched
  pair printed "Mismatched exe + license module builds … running demo").

---

## [1.6.3] — 2026-10-18

Stability release: `.hantek` v2 multi-capture files, playback/zoom improvements, the unlocked block-sampling tab, and the 50 ms/div arming fix.

### Added

- **Native `.hantek` v2:** magic `HANTEKSCOPE2\n`, float32 payload, multiple
  captures in one file, split `group/part/parts` metadata, atomic write, header
  peek; large sets split into `base_01.hantek …` sharing a group id. v1 still
  loads.
- **Waveform lozenge** shows waveform # of N (was the sample number), wrapped
  and fixed-width.
- **`.mat` import timebase** can step to 100/200 ms and beyond, with an absolute
  window (Pico-style).
- **Play reworked** to step a fraction of the window, so review playback
  animates instead of looking frozen.
- **Zoom preserved on buffer activation**, and zoom-out past the record end via
  `m_playSpan`.
- **Block Sampling tab unlocked:** Custom + target-rate ± are live and steer the
  block device clock through the `kBlockNs` ladder, persisted as
  `session/blockns`.

### Fixed

- **50 ms/div block arming** on the 1008C; every division works, with a larger
  `age` (~2.4 s) only while transitioning into/out of 50 ms/div.
- **"Cut in the drawing"** — a white gap where a min/max bucket straddled the
  paint edge; the bucketed branch now emits a connector from the previous
  bucket's last sample to the current bucket's first.
- **Multi-waveform `.hantek` reported 50004 waveforms** (sample count instead of
  waveform count).

---

## [1.6.1] — 2026-10-16

Device-foundation release: demux rule 4, per-channel V/div independence, and the first uninstaller and trigger-drag fixes.

### Added

- **Demux switch (rules 0–4)** in `HantekUsb`, with rule 4 as the default and
  shipped rule; `HS_DEMUX` remains a runtime revert.
- **Per-channel V/div independence:** each channel keeps its own side V/div, the
  grid no longer rescales when one channel changes, and dragging a channel's
  voltage moves only that channel. On start only CH1/CH2 are on.
- **Roll-frame / aliasing fixes.**

### Fixed

- **The uninstaller no longer blocks the form** on `uninstall-driver.bat`;
  progress shows during removal.
- **Trigger drag tracks the mouse** instead of lagging behind it.

### Changed

- Installer 1.6.1 built; stable snapshots taken.

---

## [1.6.0] — 2026-10-15

First installer of the line: rate caps and fallback, display trim, trigger hold/anchor, Y-spike rejection, probe-zero persistence and demo mode.

### Added

- **Rate caps and fallback:** effective-cap behaviour at 10–200 ms/div, a
  no-candidate fallback to 440 S/s (small windows), and channel on/off
  re-picking in Standard mode while Custom is preserved.
- **Display trim knob** (Sampling tab, ±20 S/s): median-3 + linear display-only
  resampling; trigger/readout keep hardware data; persists in session files.
- **Trigger handling:** clamp to the V range on all five entries, pending-edge
  anchor wait, hold-last-capture while waiting, scan-owned lozenge, compact
  clickable lozenges.
- **Y garbage rejection:** samples beyond 8 × V/div × scale are skipped by
  auto-range/data-half/refit, so bursts no longer blow the view to 700 V;
  over-range clips with a banner instead.
- **Probe-zero persistence** across restarts, re-zeroed after a probe or gain
  change.
- **Demo mode:** hardware on channel A only, settings locked to the opening
  state, view-only zoom/cursors/measurement left open.
- **Driver uninstaller** removes any 1008 device (VID_0783/name match) and our
  `ydj-2088.inf`, with a CIM fallback for silent-install recording.
- **Build stamp fix:** the title no longer lags one build; an always-run stamp
  target plus source touch makes it trustworthy.

### Changed

- Block band tuned for hardware load, then restored to 500 S per shot.

---

## Format support at a glance

| Format | Direction | First shipped |
| --- | --- | --- |
| Native `.hantek` (v1/v2/v3) | import + export | 1.6.0 line / v2 1.6.3 / v3 1.6.14 |
| PicoScope `.psdata` | import (native, in-process) | 1.6.12 |
| PicoScope `.psdata` | export | 1.6.13 line |
| CSV / MAT / TSV / text | import + export | pre-1.6.0 |
| HScope `.zip` | import + export | 1.6.24 |
| Autel MaxiScope `.json` + `.dat` | import + export | 1.6.19 |
| Autel MaxiScope `.MSData` | import + export | 1.6.24 |
| Hantek `.xls` / vendor CSV | import | 1.6.25.4 |
| USB Autoscope `.mwf` | import + export | 1.6.25 |
| ATS eSCOPE `.tdms` | import + export | unreleased (working tree) |

## Hardware support at a glance

| Device | Support added |
| --- | --- |
| Hantek 1008C | baseline |
| Hantek 6022 | 1.6.24 |
| Hantek 6000 series | 1.6.25 |
| Hantek 2090 / 2150 / 2250 | 1.6.25 |
| FX2 clones (boot firmware upload) | 1.6.25 (loader fix) |
| Autel MP408 | 1.6.25 |
| USB Autoscope | 1.6.25 |
| USBTMC / ScpiScope | 1.6.25 |
| Owon | 1.6.25 |
| PicoScope 2000A/3000A/4000A/5000A | 1.6.24 (runtime SDK loader) |
