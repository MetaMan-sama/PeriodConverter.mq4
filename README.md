# 🕐 Period Converter — `PeriodConverter.mq4`

> **MQL4 Script for MetaTrader 4**  
> Generates custom non-standard timeframe history files by aggregating OHLCV data using a configurable period multiplier, enabling offline charts at any timeframe MT4 doesn't natively support.

---

## Overview

MetaTrader 4 natively supports a fixed set of timeframes: M1, M5, M15, M30, H1, H4, D1, W1, MN. This script breaks that limitation by **synthesizing custom timeframes** from existing chart data.

By applying a period multiplier to the current chart's timeframe, the script builds a new `.hst` history file that MT4 can load as an **offline chart** — giving you access to timeframes like M2, M3, M10, H2, H3, H6, H8, and more.

Originally authored by **MetaQuotes Software Corp.** (Copyright 2006–2015), this is the canonical period converter included in many professional MT4 setups.

---

## How It Works

1. The script reads OHLCV data from the current chart bar by bar (from oldest to newest)
2. It aggregates bars into the new period using the multiplier:
   ```
   newPeriod = currentPeriod × InpPeriodMultiplier
   ```
3. Open is taken from the first bar in the group; High/Low are the extremes; Close is the last bar's close; Volume is accumulated
4. The aggregated data is written to a binary `.hst` history file:
   ```
   SYMBOL + newPeriod + ".hst"
   ```
   Located in MT4's history folder (handled automatically via `FileOpenHistory()`)
5. After running, open the offline chart via **File → Open Offline** in MT4

The script also handles **live data updates** — if `RefreshRates()` returns new bars while running, it adjusts the bar index accordingly so the output file stays current.

---

## Input Parameters

| Parameter | Default | Type | Description |
|---|---|---|---|
| `InpPeriodMultiplier` | `3` | int | Multiplier applied to the current chart timeframe |

---

## Examples

| Current Chart | Multiplier | Resulting Timeframe |
|---|---|---|
| M1 | 2 | M2 |
| M1 | 3 | M3 |
| M5 | 2 | M10 |
| M5 | 6 | M30 (same as native) |
| H1 | 2 | H2 |
| H1 | 4 | H4 (same as native) |
| H1 | 6 | H6 |
| H1 | 8 | H8 |
| D1 | 2 | 2-Day |

---

## Installation

1. Copy `PeriodConverter.mq4` to:
   ```
   MetaTrader 4/MQL4/Scripts/
   ```
2. Restart MT4 or right-click **Navigator** → **Refresh**
3. Open the chart of the **base timeframe** you want to convert (e.g., M1 for a M3 output)
4. Drag the script onto the chart
5. Set `InpPeriodMultiplier` and click **OK**
6. Wait for the script to complete (progress printed to the log)
7. In MT4 menu: **File → Open Offline** → select the new symbol+period combination

---

## Viewing the Offline Chart

After the script completes, the new chart will appear in the **Open Offline** dialog:

```
File → Open Offline → EURUSD3 (for M1 × 3 = M3)
File → Open Offline → EURUSD2 (for H1 × 2 = H2)
```

You can apply any indicator or template to the offline chart just like a live chart. To keep it updated, re-run the script periodically or use a timer-based EA wrapper.

---

## Output

The Experts log will show progress and completion:

```
48 records written
```

The `.hst` file is written to the MT4 history directory automatically via `FileOpenHistory()`.

---

## Requirements

- MetaTrader 4 (Build 600+)
- Sufficient historical bars loaded on the source chart
- `#property show_inputs` displays the input dialog before execution
- `#property strict` compliance (enforced)

---

## Notes

- The script uses `FILE_SHARE_WRITE | FILE_SHARE_READ` flags — multiple instances can run simultaneously for different symbols
- If the history file already exists, it is overwritten with fresh data
- The `OnDeinit()` function ensures the file handle is properly closed on script exit
- For automated periodic updates, wrap this script's logic inside an EA with a timer

---

## Copyright

Original script by **MetaQuotes Software Corp.**  
Copyright © 2006–2015 — [http://www.mql4.com](http://www.mql4.com)

---

## Disclaimer

This script is provided as-is for educational and practical use. Custom offline charts are derived data and may have minor discrepancies from broker-native feeds. Always verify price data before making trading decisions.

---

## License

Original: MetaQuotes Software Corp. license. Modifications and redistribution subject to applicable terms.
