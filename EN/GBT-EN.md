# GBT — Gamma Bomb Trap

## Read the Battlefield: WHERE (price levels) + WHEN (session timing)

GBT transforms CME Open Interest and Intraday Volume data into a visual map of dealer hedging pressure overlaid with Quarterly Theory session timing. Know where mechanical forces exist and when the market is most likely to move.

---

## 📥 Inputs

### Data Input

| Input | What it is | How to use |
|---|---|---|
| **Future Symbol** | The futures contract to analyze | Select from dropdown, e.g. `COMEX:GC1!` |
| **OI Data CSV** | Open Interest from CME (end-of-day positions) | Copy entire contents of `OIData.txt` and paste |
| **Intraday Volume CSV** | Contracts traded today from CME | Copy entire contents of `IntradayData.txt` and paste |

Both files share the same format: line 1 = header (DTE, spot price), line 2 = column names, line 3+ = `Strike, Call, Put, IV` data.

### Price Grid

| Input | Description | Recommended |
|---|---|---|
| **Major Level Step** | Grid spacing for snapping strike prices | Gold = `25` |

### GEX / Model

| Input | Description | Recommended |
|---|---|---|
| **Risk-Free Rate** | Interest rate for Black-76 model | `0.045` |
| **Contract Multiplier** | Contract size (Gold = 100 troy oz) | `100` |
| **Heatmap lookback** | Number of bars drawings extend backward | `48` |
| **Intraday Vol weight (α)** | Blend ratio between OI and intraday volume | `0.4` |
| **Block Trade trigger** | Min Vol/OI ratio to flag a block trade | `2.0` |

Composite GEX formula: `GEX = GEX_OI × (1−α) + GEX_Vol × α`

### Quarterly Theory

| Input | Description |
|---|---|
| **Show Session Backgrounds** | Color the chart background by session |
| **Show True Open Lines** | Plot the opening price of each session |
| **Show 90-min Quarter borders** | Show quarter division lines within sessions |
| **Timezone** | Session calculation timezone (default: `America/New_York`) |

### Display

| Input | Description |
|---|---|
| **Label Mode** | `Compact` = short label, hover for detail. `Full` = show everything |
| **GEX Heatmap** | Color boxes showing GEX intensity at every price level |
| **Dealer Hedge Zones** | Boxes at strikes with highest dealer hedging pressure |
| **Gamma Flip Zone** | Yellow box where net GEX crosses zero |
| **Max Pain** | Price where aggregate option buyer loss is maximized |
| **Call/Put Walls** | Resistance (Call Wall) and Support (Put Wall) lines |
| **Vanna & Charm** | Dashed lines showing pressure from IV changes and time decay |
| **Volume Pressure** | Label showing net call vs put volume bias |
| **Block Alerts** | Box + label when block-sized trades are detected |
| **Dashboard Table** | Summary table of all layers |

### Colors — every element is customizable.

---

## 📊 Chart Drawings

### Horizontal Lines

| Line | Color | Style | Meaning |
|---|---|---|---|
| **CW (Call Wall)** | 🟢 Lime | Solid | Resistance — strike above spot with highest positive net GEX. Dealers sell futures here. |
| **PW (Put Wall)** | 🔴 Red | Solid | Support — strike below spot with highest absolute net GEX. Dealers buy futures here. |
| **MP (Max Pain OI)** | 🟣 Purple | Solid | Price where option buyers lose the most (from OI). Gravitational pull intensifies near expiry. |
| **MPv (Max Pain Vol)** | 🟣 Light Purple | Dashed | Max Pain from intraday volume — shows intraday shifts. |
| **Vanna** | 🔵 Cyan | Dashed | IV-driven hedging direction. "IV↑=SELL" means rising IV forces dealers to sell futures. |
| **Charm** | 🟠 Orange | Dashed | Time-driven hedging direction. "T=SELL" means time decay forces dealers to sell futures. |

### Boxes

| Box | Color | Meaning |
|---|---|---|
| **Dealer Hedge Zone** 🟩 | Green (transparent) | Long Gamma — dealers sell rallies / buy dips → suppresses volatility, price mean-reverts |
| **Dealer Hedge Zone** 🟥 | Red (transparent) | Short Gamma — dealers buy rallies / sell dips → amplifies volatility, price trends |
| **Gamma Flip** 🟡 | Yellow (transparent) | Zero-GEX boundary — above = Long Gamma regime, below = Short Gamma regime |
| **Block Alert** 💜 | Fuchsia | Block-sized trade detected — "CALL Block" = institutional call buying, "PUT Block" = put buying |
| **GEX Heatmap** | Green/Red (intensity-scaled) | GEX magnitude at every price level (off by default) |

### Background Colors (Quarterly Theory Sessions)

| Background | Session | Time (EST) | Volatility |
|---|---|---|---|
| Dark gray, very faint | **Sydney** | 17:00–23:00 | Lowest |
| Light pink | **Tokyo** | 23:00–05:00 | Moderate |
| Light blue | **London** | 05:00–11:00 | High |
| Light green | **New York** | 11:00–17:00 | High |
| Near-transparent white | **High Vol Zone** | London Q3-Q4 + NY Q1-Q2 | ⚡ Highest |

### Session True Open Lines (plot.style_linebr)

| Line | Color | Meaning |
|---|---|---|
| **Sydney Open** | Gray | Opening price of Sydney session — intraday bias reference |
| **Tokyo Open** | Pink | Opening price of Tokyo session |
| **London Open** | Blue | Opening price of London session |
| **NY Open** | Green | Opening price of New York session |

Price above True Open = bullish session bias. Price below = bearish session bias.

### Bar Color

| Color | Meaning |
|---|---|
| 🟢 Green | Futures bar closed above open (up bar) |
| 🔴 Red | Futures bar closed below open (down bar) |

### Volume Pressure Label

| Label | Meaning |
|---|---|
| **VOL C 65%** | Call volume is 65% of total → bullish intraday flow |
| **VOL P 70%** | Put volume is 70% of total → bearish intraday flow |

---

## 📋 Dashboard Table

| Row | Data | Example | Description |
|---|---|---|---|
| **Regime** | LONG γ / SHORT γ | `LONG γ` `Mean-Revert` | Positive net GEX = dealers suppress moves. Negative = dealers amplify moves. |
| **Net GEX** | Aggregate GEX value | `+2.45M` | Larger absolute value = stronger hedging pressure |
| **CW / PW** | Call Wall / Put Wall | `5260 / 5100` | R = Resistance, S = Support |
| **Flip / MP** | Gamma Flip / Max Pain | `5152 / 5150` | "Above flip" = price is in Long Gamma territory |
| **Session** | Session + QT phase | `LON` `Q3 Manip ⚡` | Current session + XAMD cycle phase + high-vol flag |
| **V / C** | Vanna / Charm flow | `+1.2K / -0.8K` | Direction of hedging pressure from IV change and time decay |
| **DTE** | Days to Expiry | `V:13.2h` `OI:0.55d` | Time remaining until expiry (V = from intraday data, used for Greeks) |

---

## 🔄 Quarterly Theory — XAMD Cycle

Each 6-hour session is divided into 4 quarters × 90 minutes:

| Quarter | Phase | Behavior |
|---|---|---|
| **Q1** | X — Continuation/Reversal | Carries over from previous session or begins reversing |
| **Q2** | A — Accumulation | Builds a range, consolidation |
| **Q3** | M — Manipulation | ⚠️ Judas Swing — liquidity sweep, false breakout |
| **Q4** | D — Distribution | ✅ Real move — trend expansion |

---

## ⚠️ Notes

- OI/Volume data must be re-pasted when new CME data is available.
- GEX assumes dealers are the counterparty (correct for most cases, but not 100%).
- When DTE is very low (< 1 day), gamma becomes extreme — walls are strong but shift quickly.
- For entry triggers (CRT + VWAP layers), use **GBT-Flow**.