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

## Key Insights

*(To be filled in after running on the full dataset — see Section 6 of the notebook.)*

- Average PnL by sentiment:
- Win rate by sentiment:
- Position sizing by sentiment:
- Direction bias by sentiment:
- % of trades outside Fear/Greed date coverage:

## Suggested strategy implications

*(To be filled in based on the actual results — see Section 7 of the notebook.)*