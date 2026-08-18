# Experimental FT2 support (this fork only)

This branch is a **local experiment**. It is not a pull request against alexranaldi/CWSL_DIGI.

## How to finish the source changes (no pasting)

Headers are already committed. The remaining hunks are in `experimental-ft2.patch`.
From a clone of this branch:

```
git checkout experimental-ft2
git apply experimental-ft2.patch
```

That updates `source/CWSL_DIGI.cpp`, `source/DecoderPool.hpp`, and `source/OutputHandler.cpp` in one step. You do not copy/paste diffs.

## What this does

Adds a decoder mode string `FT2`:

- T/R period **3.75 s** (16 slots per minute)
- Timing thread `waitForTimeFT2`
- Audio capture / queueing like FT4/FT8
- Output parsing reuses the FT4/FT8 77-bit text parser
- Also accepts Improved-style decode marker `*`

## What this does **not** do

CWSL_DIGI still calls **jt9.exe** from `wsjtx.binpath`. Stock official WSJT-X jt9 has **no FT2**.

You must point `wsjtx.binpath` at a bin folder whose `jt9.exe` actually decodes FT2:

- **MSHV / Decodium** (same FT2 family as each other)
- **or** WSJT-X Improved (different waveform — not interoperable with Decodium/MSHV)

Pick **one** dialect. They cannot decode each other.

## Config example

```
# 20m experimental FT2 (check live QRG; not IARU-standard)
#decoder=14084000 FT2
```

Use USB dial frequencies your chosen software actually uses.

## jt9 flags (starting guess)

The code invokes jt9 like FT4 (`-5`) and sets `ntrperiod` to 4 (struct field is an int).
If your Improved/MSHV jt9 needs a different switch, change `source/DecoderPool.hpp`.

If shared-memory decode fails, set:

```
transfermethod=wavefile
```

## Timing

FT2 needs a tight PC clock (±50 ms class). If DT is wild, fix NTP/GPS first.

Keep `maxdataage` modest; 3.75 s cycles go stale quickly.

## Reporting

PSK Reporter / RBN will receive mode string `FT2` if those outputs are enabled.
Aggregator/JTAlert may not fully understand FT2.
