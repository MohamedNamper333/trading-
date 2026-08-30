# EURUSD M1 Training Dataset — Verification Report

## Status
The 18-part `EURUSD_M1_TRAINING_READY` dataset was re-scanned after splitting.

## Aggregate integrity
- Parts: 18
- Total rows: 8,220,892 (excluding repeated headers)
- Timestamp order: strictly increasing
- Duplicate timestamps: 0
- OHLC invariant violations: 0
- Long flat runs (>=60 consecutive minutes): 0
- Remaining flat candles: 100,533
- Part-boundary continuity: every adjacent part continues exactly 1 minute after the previous part's final timestamp.

## Coverage
- First timestamp: 2003-05-04 21:00:00 UTC
- Last timestamp: 2026-08-28 20:59:00 UTC
- Calendar years represented: 2003–2026

## Known data treatment
The training-ready file was produced from the previously audited raw EURUSD M1 Bid file:
1. 653 OHLC-invalid bars identified in the raw source were excluded.
2. Long runs of flat/placeholder bars (>=60 minutes) were excluded from the training-ready series.
3. Raw source remains preserved separately.
4. No forward-fill or interpolation was used.

## Important limitation
The dataset is still **Bid-only** and therefore does not yet contain Ask, Spread, or Tick Volume. It is a high-quality historical price core, not yet the final multi-source training dataset.

## Next research stage
Build the feature/context layers only after the raw price core is frozen:
- market structure
- indicators
- sessions
- macro/rates
- positioning
- economic events
- cross-market context

All context data must be aligned by point-in-time availability to prevent look-ahead leakage.
