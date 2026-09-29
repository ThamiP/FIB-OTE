# FIB-OTE — Expansion Leg Fibs + ICT PD Arrays (Pine Script v6)

A TradingView indicator that draws a Fibonacci retracement on the most recent **expansion leg**. On that leg it maps ICT PD arrays: **FVG, IFVG, BPR, OB, Breaker (BB), CISD, CISD+OB, MSS and MSS FVG**. It only draws arrays that sit in the leg's **50% area** or **OTE zone**.

## Install

1. Open TradingView and go to **Pine Editor**.
2. Paste the contents of [`FIB-OTE.pine`](FIB-OTE.pine).
3. Click **Add to chart**.

## How legs work

A bullish leg runs from its **anchor** (swing low, Fib `1`) to its **extreme** (high, Fib `0`). Bearish legs are the mirror image.

| Event | What happens |
|---|---|
| Price keeps making new extremes | The leg is still **expanding**, so it extends and the Fib stretches with it. |
| No new extreme for *Leg swing length* bars (or a deep pullback) | The extreme **locks** and the leg is now **retracing**. |
| A locked extreme is broken after a real pullback | A **new leg** (continuation) starts. It is anchored at the pullback low/high. A pullback counts as real when it held for the swing length or retraced at least *Deep pullback re-anchors at*. |
| A locked extreme is broken after a shallow, fast pullback | The current leg is **extended** instead of replaced. |
| Price breaks the anchor (*Leg fails beyond ratio*, default `1.0`) | The leg **fails**. It is dimmed and marked `✕`, and a **new opposite leg** starts from the old extreme or from any sweep beyond it. |

With *Break confirmation* set to **Close**, wick sweeps of the extreme or the anchor are ignored. Only a candle close counts.

The active leg is drawn in full. Older legs stay on the chart, dimmed, up to *Legs kept on chart*.

## PD arrays

An array is drawn only if it:
1. formed on the active leg (at or after the leg's anchor),
2. points in the leg's direction (bullish arrays on bullish legs), and
3. sits in the selected zone. The default is **50% area (0.45–0.55) or OTE (0.618–0.79)**. The match rule can be *overlaps*, *midpoint inside* or *fully inside*.

| Array | Definition used |
|---|---|
| **FVG** | 3-candle gap: `low > high[2]` (bull) / `high < low[2]` (bear). There is an optional minimum size in ATR. |
| **IFVG** | An opposing FVG that a candle closes through. It flips and acts in the leg direction. |
| **BPR** | The overlap of a new FVG with an opposing FVG from the last *N* bars. |
| **OB** *(off by default)* | The last opposite-close candle before a displacement that printed an FVG. |
| **Breaker (BB)** | Internal swing high → lower low that sweeps the prior internal low → close above the swing high. The breaker is the up-close candle at that swing high. Bearish is mirrored. |
| **CISD** | Change in State of Delivery: a close through the **open of the first candle** in the opposing delivery series (the run of down-close candles into the low). It is drawn as a level. |
| **CISD+OB** | The order block formed by that same delivery series, from the first candle's open to the series extreme. The CISD confirms it. |
| **MSS** | An internal-structure break against the prevailing internal trend, i.e. the shift at the end of a retracement. It is drawn as a level. |
| **MSS FVG** | An FVG printed inside the MSS displacement, or up to *N* bars after it: the "MSS + PD FVG" entry. |

Arrays are **invalidated** when a candle closes through them against the leg. Invalidated arrays are dimmed or hidden, depending on the setting. With *Skip overlapping duplicates* on, a new array is not drawn when a live array of the same type already covers that price.

**Range settings:** *OB / breaker range* switches between wick (full candle range) and body.

## Dashboard

The dashboard shows:
- leg direction, number and state (expanding or retracing)
- anchor → extreme and the leg size in ATR
- where price sits in the leg (retracement ratio plus premium / 50% / discount / OTE)
- the OTE and 50% prices
- a count of live in-zone arrays by type

## Alerts

- **`alertcondition` events:** New bullish leg, New bearish leg, Leg failed, Price reached OTE, Price reached 50% area, New PD array in zone, PD array in zone tapped.
- **Detailed messages:** create an alert on **Any alert() function call** to get text such as `▲ CISD+OB formed in OTE of leg 12 @ 1.0712 – 1.0720`.

The data window also exposes the active leg's anchor, extreme, equilibrium, OTE bounds and direction. You can use them in custom alerts.

## Key settings

| Setting | Default | Notes |
|---|---|---|
| Leg swing length | 6 | Bigger values give fewer, larger legs. |
| Break confirmation | Close | `Wick` counts sweeps. |
| Deep pullback re-anchors at | 0.382 | Lets fast V-shaped pullbacks start a new leg. |
| Min new-leg size | 1.5 × ATR | A smaller continuation extends the current leg instead. |
| Leg fails beyond ratio | 1.0 | Use `0.886` to invalidate earlier. |
| Retracement levels | `0, 0.5, 0.618, 0.705, 0.79, 1` | Comma separated. Optional extension targets `-0.27, -0.62, -1`. |
| Arrays must sit in | 50% + OTE | Also available: OTE only, 50% only, discount/premium half, anywhere in leg. |
