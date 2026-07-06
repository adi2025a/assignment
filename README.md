# Trader Performance vs. Bitcoin Market Sentiment

Analysis of whether Hyperliquid trader performance (PnL, win rate, position size, trade
direction) differs systematically across Bitcoin Fear & Greed market regimes.

## Files

| File | Description |
|---|---|
| `trader_sentiment_analysis.ipynb` | Main analysis notebook |
| `fear_greed_index.csv` | Daily Fear/Greed classification (Date, Value, Classification) |
| `historical_data.csv` | Hyperliquid trade-level execution data *(replace sample with your full file)* |

## How to run

1. Place `fear_greed_index.csv` and your full `historical_data.csv` in the same folder as the notebook.
2. Open `trader_sentiment_analysis.ipynb` in Jupyter or Google Colab.
3. In the first code cell, set:
   ```python
   TRADER_FILE = 'historical_data.csv'
   ```
4. Run all cells top to bottom.

## Approach

1. **Load** both datasets and inspect shape/dtypes.
2. **Clean the Fear/Greed data** — parse dates, keep `date`, `value`, `sentiment`.
3. **Clean the trade data** — standardize column names, parse `Timestamp IST`, drop duplicates,
   check for missing/invalid values (zero or negative size/price).
4. **Fix the date-alignment issue** — trade timestamps are in IST (UTC+5:30) but the
   Fear/Greed index is keyed to UTC calendar days. Converting IST → UTC before deriving
   the join date prevents late-night trades from being tagged with the wrong day's sentiment.
5. **Merge** trades to sentiment on the corrected UTC date, and explicitly report how many
   trades fall outside the Fear/Greed index's date coverage (Hyperliquid trade history may
   extend beyond or fall short of the index's 2018–2025 range).
6. **Exploratory analysis**: average/median PnL, win rate, average position size, and
   buy/sell mix, each broken down by sentiment class; cumulative PnL vs. sentiment value
   over time; per-coin PnL by sentiment.
7. **Insights & strategy implications** — translated into plain-language takeaways a
   trading strategy could act on.

## Known data-alignment caveat

The two datasets don't perfectly overlap in date range and use different timezones/date
grains (IST trade timestamps vs. UTC daily sentiment). The notebook makes this explicit
rather than silently dropping mismatched rows — see the "unmatched" and "mismatch" counts
printed in Sections 3–4.

**Confirmed on the full dataset (211,224 trades):**
- Trade data spans **2023-04-30 to 2025-05-01**, a subset of the Fear/Greed index's full
  2018-02-01 to 2025-05-02 coverage — so every trade has a matching sentiment day
  (**0 unmatched out of 211,224**).
- The IST → UTC timezone fix genuinely mattered: **53,322 of 211,224 trades (25.2%)**
  had their calendar day shift once converted to UTC. Roughly 1 in 4 trades would have
  been joined to the wrong sentiment day without this correction.
- 43 rows have non-positive `Size USD` — small dust/settlement-type entries, flagged but
  not removed (they don't materially affect the aggregates).

## Key Insights

- **Average PnL by sentiment:** highest during **Extreme Greed** (mean **$65.09**/trade,
  n=40,180) and **Extreme Fear** ($50.34, n=21,303); lowest during **Neutral** ($32.91,
  n=39,563). Plain Fear ($46.63) and Greed ($50.12) sit in between. Emotional *extremes*
  outperform the calmer Neutral regime on average PnL.
- **Win rate by sentiment:** highest in **Extreme Greed** (**46.3%**) and roughly tied
  between Extreme Fear (41.8%) and Fear (42.1%); lowest in **Neutral** (36.3%) and Greed
  (39.3%). The pattern echoes the PnL result — traders in this dataset do relatively
  better during strong sentiment (either direction) and worse when the market is flat.
- **Direction bias:** traders open noticeably more **long** exposure during Fear/Extreme
  Fear (Open Long share ~27–28%) and shift toward more **short** exposure during Greed
  (~23% Open Short vs. ~15% Open Long). This looks contrarian — buying into fear, hedging
  or fading into greed — rather than pure sentiment-following.
- **Date coverage:** 0% of trades fell outside the Fear/Greed index's range; the
  constraint is the trade data's own 2023–2025 window, not a gap in the sentiment data.
- **Per-coin variation:** PnL-by-sentiment varies a lot by coin (see `top_coins` in the
  notebook) — some coins (e.g. `@107`) show a huge swing from strongly negative in
  Extreme Fear to strongly positive in Extreme Greed, suggesting sentiment sensitivity is
  concentrated in a subset of traded assets rather than uniform across the book.

## Suggested strategy implications

- Since both **Extreme Fear and Extreme Greed** show better average PnL and win rate
  than Neutral, a strategy might look for **increased position-taking during high-
  conviction sentiment regimes** and **reduced size during Neutral** periods, where edge
  appears weakest.
- The contrarian direction pattern (longs into Fear, shorts into Greed) suggests the
  traders in this dataset skew toward **mean-reversion / dip-buying-fading-rallies**
  rather than momentum. Worth testing whether this behavior is *causing* the better
  extreme-regime performance, or just correlated with it.
- The per-coin sensitivity suggests a **sentiment-conditioned coin filter** (sizing up
  the coins that historically respond most positively to extreme sentiment shifts) could
  add value, but this needs out-of-sample validation before being treated as a rule.
- As always: sentiment alone is a weak, low-frequency signal (one value per day). Treat
  these as directional hypotheses to combine with price/volatility features, not a
  standalone trading rule.