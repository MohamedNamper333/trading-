# Trading Agent — Dataset Specification

**Status:** Drafted after source and period review  
**MVP market:** EURUSD  
**Canonical market source:** Dukascopy historical tick data  
**Primary decision clock:** 15 minutes  
**Data policy:** point-in-time, leakage-controlled, time-ordered

## 1. Objective

Build a high-quality historical dataset for training and evaluating an adaptive trading agent locally on GTX 1660 Super 6GB VRAM + 24GB RAM.

The dataset must prioritize market-regime coverage, execution realism, timestamp integrity, and point-in-time availability over raw row count.

## 2. Primary market data

### Dukascopy

Use the longest **continuous, verifiable, high-quality EURUSD tick history** available from Dukascopy, subject to the quality gates defined below.

Dukascopy documentation exposes historical tick retrieval and identifies the first available data for each feed. A current Dukascopy data index reports EURUSD tick availability beginning **2003-05-04 21:00 UTC**; this date is a source reference, not an unconditional training start date. The final start date must be determined after the quality audit.

### Raw fields

- UTC timestamp with millisecond precision when supplied
- Bid price
- Ask price
- Source identifier

### Derived execution fields

- Spread
- Mid price
- Bid/ask changes
- Tick arrival intensity
- Local spread statistics

## 3. Canonical timeframes

The canonical 1-minute series is reconstructed from validated ticks. Higher timeframes are derived from the canonical series.

- 1m
- 5m
- 15m
- 1H
- 4H
- Daily

The model's primary decision clock is 15m. Higher and lower resolutions are contextual/analytical rather than independent data sources.

## 4. Market-structure features

Derived from price data rather than downloaded as external signals:

- swing highs/lows
- HH / HL / LH / LL
- trend structure
- break of structure
- change of character
- breakout / failed breakout
- retest
- rejection
- compression / expansion
- range boundaries
- liquidity-sweep proxies
- momentum and price-velocity features

## 5. Technical feature layer

Initial library:

- EMA 20 / 50 / 100 / 200
- SMA 20 / 50 / 100 / 200
- RSI with contextual and multi-timeframe representations
- MACD
- ADX / DI
- ATR
- Bollinger Bands, %B, bandwidth
- Stochastic
- MFI
- ROC / momentum
- Donchian / Darvas-style range features
- volatility statistics

Indicators must not be converted into naive fixed rules such as `RSI < 30 = BUY`. Features should represent level, slope, regime, divergence, multi-timeframe relationship, and contextual interaction where appropriate.

## 6. Session and calendar context

All canonical timestamps remain UTC. Local market sessions are derived from UTC using explicit timezone rules.

Required context:

- Asia session
- London session
- New York session
- London/New York overlap
- session transitions
- hour of day
- day of week
- month/quarter boundaries where relevant

## 7. Liquidity and execution proxies

Because spot FX is fragmented OTC and has no single global order book, the dataset must not claim to measure global FX liquidity directly.

Use measurable proxies:

- bid/ask spread
- spread percentile
- spread expansion/contraction
- tick arrival intensity
- volatility/spread relationship
- price-impact proxies
- abnormal spread state
- liquidity deterioration/recovery flags

## 8. Positioning data

### CFTC COT

Use EUR futures positioning as a positioning/context layer, not as a direct entry signal.

Candidate fields:

- commercial/dealer-related positioning where available
- managed-money positioning
- other reportable positioning
- long contracts
- short contracts
- spreading where available
- net position
- week-over-week position change
- open interest
- normalized/extreme positioning measures

Historical Disaggregated COT machine-readable data begins 2006-06-13. Historical classification backcasting has documented limitations and must be treated accordingly.

Every observation must carry its actual publication/availability timestamp so the agent cannot see information before release.

## 9. Macro and rates

### Federal Reserve

- FOMC decisions
- meeting dates
- policy-rate changes
- statement/release timestamps

### ECB

- monetary-policy decisions
- policy-rate changes
- euro-area yield curve

ECB daily euro-area yield-curve data is available from 2004-09-06 onward.

### United States

- US 2Y yield
- US 10Y yield
- rate/yield changes
- selected inflation, employment, growth and consumption series

### Euro area

- selected inflation, activity and rates series
- euro-area yield curve points

Derived cross-market variables:

- US 2Y minus EU 2Y
- US 10Y minus EU 10Y
- change in yield differentials
- monetary-policy divergence proxies

FRED/ALFRED should be preferred for historical macro observations when a vintage/real-time representation is required. ALFRED preserves what was known at earlier points in time and therefore helps prevent revision leakage.

## 10. High-impact economic events

Required event fields:

- event identifier
- currency/region
- event type
- scheduled timestamp
- release timestamp when known
- importance
- previous
- forecast
- actual
- surprise
- time to release
- time since release
- availability flag

The agent must not receive `actual` before the actual release timestamp.

Primary official sources include Federal Reserve, BLS and BEA release calendars/data, plus ECB releases for euro-area events.

## 11. Cross-market context — MVP

The following markets are context features rather than primary trading targets:

- DXY / broad USD-strength proxy
- Gold
- S&P 500
- VIX

They should be represented mainly through normalized returns, momentum, volatility, shock and regime features rather than raw prices alone.

Additional EUR crosses and FX pairs are deferred until the EURUSD MVP demonstrates generalization.

## 12. News/NLP

News text and LLM/NLP sentiment are **Phase 2**. The MVP prioritizes chart/market structure, rates, events and positioning.

## 13. Strategy knowledge dataset

Strategy knowledge is maintained separately from market observations. Initial strategy families:

- Bollinger
- Wyckoff
- Darvas Box
- Price Action
- Trend Following
- Breakout
- Momentum
- Mean Reversion
- Wave-based approaches

Each strategy definition should eventually include:

- inputs
- conditions
- entry concepts
- exit/invalidation concepts
- preferred regimes
- weak regimes
- known failure modes
- parameters

The agent may later combine, modify and discover rules rather than being limited to selecting one predefined strategy.

## 14. Data quality gates

Every dataset release must pass:

1. timestamp integrity
2. duplicate classification
3. Bid/Ask validity
4. spread validity
5. gap classification
6. abnormal-price audit
7. session/weekend validation
8. DST/timezone validation
9. cross-source sanity checks where available
10. point-in-time availability audit
11. feature leakage audit

Statistical rarity alone must never be treated as proof of bad data. Real market shocks must be preserved when the evidence supports them.

## 15. Storage policy

Keep raw market data immutable and store processed data as versioned, partitioned artifacts. Do not load the entire historical dataset into RAM during training.

Recommended logical layers:

`raw → normalized → canonical → features → contexts → training snapshots`

## 16. Training eligibility

Every artifact must be tagged as one of:

- `TRAINABLE`
- `CONTEXT_ONLY`
- `EVALUATION_ONLY`

The final hidden test period must never be used for model selection or feature tuning.

## 17. Current period policy

Target the longest continuous high-quality EURUSD tick history available from Dukascopy, with the current reference availability beginning 2003-05-04 21:00 UTC. Do not force the full period into training until continuity and quality are verified.

A practical starting split is expected to use an early multi-year training period, a later validation period, rolling walk-forward periods, and a final locked hidden period. Exact calendar boundaries are defined only after the downloaded dataset passes the quality audit.
