# Experimental FT2 support (this fork only)

Private experiment on `experimental-ft2`. Do **not** open a PR against alexranaldi/CWSL_DIGI.

## Decoder choice: option 2 — WSJT-X Improved

CWSL_DIGI still launches `jt9.exe` from `wsjtx.binpath`.

This branch is set up for **WSJT-X Improved 3.1.0 or later** (DG2YCB).
Stock official WSJT-X has no FT2. MSHV has FT2 but is not a `jt9.exe`.

1. Install Improved 3.1+ from https://sourceforge.net/projects/wsjt-x-improved/files/
2. Point CWSL at its `bin` folder, for example:

```
wsjtx.binpath=C:\WSJT\wsjtx_improved\bin
```

3. Confirm that folder contains `jt9.exe` from Improved, not stock WSJT-X.

Improved marks FT2 decodes with `*`. This fork accepts `~`, `+`, and `*`.

## What the code does

- Mode string `FT2`, T/R period **3.75 s** (16 slots per minute)
- Timing thread `waitForTimeFT2`
- Capture / queueing same path as FT4/FT8
- jt9 invoked like FT4 (`-5`); shared-memory `nmode=5`, `ntrperiod=4`
- Output parsing reuses the 77-bit FT4/FT8 parser

If Improved later adds a dedicated jt9 switch for FT2, change `source/DecoderPool.hpp`.

If shared-memory decode fails:

```
transfermethod=wavefile
```

## Config example

```
# 20m experimental FT2 — confirm live QRG in Improved first
#decoder=14084000 FT2
```

Keep `maxdataage` modest; 3.75 s cycles go stale quickly.
FT2 needs a tight PC clock (±50 ms class).

## Reporting

PSK Reporter / RBN get mode string `FT2` if those outputs are enabled.
JTAlert support for FT2 is limited.
