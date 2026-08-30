# EURUSD M1 Training-Ready — Initial Analysis

## Dataset integrity

- Parts: 18
- Rows: 8,220,892
- Timestamp: Unix milliseconds, UTC
- Range: 2003-05-04 21:00 UTC → 2026-08-28 20:59 UTC
- Duplicate timestamps: 0
- Non-monotonic timestamps: 0
- Invalid OHLC bars: 0 after the prior cleaning step
- Long flat-fill blocks: removed in Training-Ready; remaining flat bars: 100,533

## Important correction

The earlier timestamp anomaly was caused by interpreting Unix-millisecond timestamps as ISO datetimes during an inspection pass. Re-parsing the stored timestamps correctly confirms the dataset date range above. This did not modify the files.

## Year coverage

| Year | Bars | First Open | Last Close | High | Low | 1m Return Std | Flat Bars |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 2003 | 245953 | 1.12284 | 1.25940 | 1.26455 | 1.07606 | 0.00021682 | 35783 |
| 2004 | 374013 | 1.25934 | 1.35523 | 1.36649 | 1.17573 | 0.00020009 | 49264 |
| 2005 | 370863 | 1.35464 | 1.18439 | 1.35777 | 1.16404 | 0.00017760 | 56882 |
| 2006 | 374400 | 1.18436 | 1.31913 | 1.33653 | 1.18088 | 0.00014366 | 50242 |
| 2007 | 374400 | 1.31908 | 1.45844 | 1.49646 | 1.28668 | 0.00013878 | 61350 |
| 2008 | 365760 | 1.45841 | 1.39707 | 1.60389 | 1.23290 | 0.00023848 | 35898 |
| 2009 | 373020 | 1.40641 | 1.43283 | 1.51441 | 1.24560 | 0.00022081 | 57797 |
| 2010 | 318240 | 1.43283 | 1.33852 | 1.45797 | 1.18989 | 0.00021676 | 20887 |
| 2011 | 319138 | 1.33436 | 1.29578 | 1.49398 | 1.28581 | 0.00021256 | 69394 |
| 2012 | 314978 | 1.29368 | 1.32003 | 1.34857 | 1.20419 | 0.00015953 | 70184 |
| 2013 | 322887 | 1.31936 | 1.37410 | 1.38932 | 1.27468 | 0.00014341 | 81074 |
| 2014 | 326397 | 1.36653 | 1.21613 | 1.39933 | 1.21239 | 0.00011858 | 92808 |
| 2015 | 340249 | 1.20871 | 1.08564 | 1.20890 | 1.04624 | 0.00021443 | 79474 |
| 2016 | 321943 | 1.08730 | 1.05150 | 1.16162 | 1.03523 | 0.00016858 | 70898 |
| 2017 | 352099 | 1.05148 | 1.19984 | 1.20923 | 1.03403 | 0.00012999 | 81855 |
| 2018 | 354827 | 1.20102 | 1.14644 | 1.25553 | 1.12156 | 0.00013240 | 77869 |
| 2019 | 343051 | 1.14598 | 1.12076 | 1.15697 | 1.08790 | 0.00009449 | 84169 |
| 2020 | 370500 | 1.12136 | 1.22141 | 1.23101 | 1.06357 | 0.00014184 | 81639 |
| 2021 | 372902 | 1.22396 | 1.13667 | 1.23494 | 1.11859 | 0.00009616 | 97987 |
| 2022 | 368731 | 1.13668 | 1.07026 | 1.14948 | 0.95357 | 0.00018028 | 74275 |
| 2023 | 362953 | 1.06970 | 1.10374 | 1.12756 | 1.04483 | 0.00012715 | 87853 |
| 2024 | 355600 | 1.10427 | 1.03526 | 1.12140 | 1.03325 | 0.00010521 | 93699 |
| 2025 | 360427 | 1.03503 | 1.17455 | 1.19186 | 1.01772 | 0.00014171 | 77420 |
| 2026 | 237561 | 1.17387 | 1.15828 | 1.20822 | 1.13245 | 0.00010977 | 54989 |

## Decision

This is a valid **EURUSD M1 price core** for further feature engineering. It is not the final execution-aware training dataset because it has no Ask, spread, or tick volume. Higher timeframes should be derived from this canonical M1 series rather than downloaded separately.
