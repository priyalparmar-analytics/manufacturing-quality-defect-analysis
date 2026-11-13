# Data

## Source

The **SECOM** dataset from the
[UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/179/secom):
sensor readings from a semiconductor manufacturing line, with a pass/fail
result for each production unit.

| File | Contents |
| --- | --- |
| `secom.data` | 1,567 rows × 590 sensor readings, whitespace-separated, no header |
| `secom_labels.data` | Pass/fail label and timestamp per unit, in the same row order |
| `secom.names` | The dataset's original description from UCI |

## Coverage

| Property | Value |
| --- | --- |
| Production units | 1,567 |
| Sensors per unit | 590 |
| Failures | **104 (6.64%)** |
| Passes | 1,463 |
| Period | 19 July 2008 11:55 – 17 October 2008 06:07 |
| Missing sensor readings | **41,951** `NaN` values |

## Columns

**`secom.data`** has no header. The notebook names the columns `sensor_0`
through `sensor_589`. **The sensor identities are not published** — UCI
anonymised them — so no reading can be tied to a named physical measurement.
That is a property of the dataset, and it limits every conclusion about *cause*.

**`secom_labels.data`** has two fields:

| Field | Values | Notes |
| --- | --- | --- |
| `label` | `-1` pass, `1` fail | Recoded in the notebook to `Fail` = 1/0 |
| `timestamp` | `DD/MM/YYYY HH:MM:SS` | The only time information available |

## What the dataset does **not** contain

Worth stating up front, because it bounds what can be concluded:

- **No machine, line or tool identifier** — failures cannot be traced to equipment.
- **No shift or operator column.** The Morning / Evening / Night split in this
  analysis is **derived from the hour of the timestamp** and is an assumption,
  not recorded fact.
- **No raw-material, recipe or batch data.**
- **No sensor names or units**, so a flagged sensor cannot be interpreted physically.

## Cleaning

| Step | Sensors removed | Reason |
| --- | ---: | --- |
| More than 40% missing | 32 | Too sparse to impute credibly |
| Constant across all units | 116 | Zero variance, zero information |
| **Retained** | **442** | Remaining gaps filled with the column median |

## Derived features

| Feature | Built from | Notes |
| --- | --- | --- |
| `date`, `hour`, `weekday`, `week` | `timestamp` | ISO week is the unit of the p-chart |
| `shift` | `hour` | Morning 06–14, Evening 14–22, Night 22–06 — **an assumption** |
| `Fail` | `label` | 1 where `label == 1` |

## Generated output

The notebook writes `../outputs/powerbi_tables.xlsx`, one sheet per table, and
the Power BI dashboard reads that workbook:

| Sheet | Contents |
| --- | --- |
| `units` | Unit-level records with time features and the pass/fail flag |
| `weekly_pchart` | Weekly failure rate with control limits |
| `shift_summary` | Counts and failure rate per shift |
| `sensor_ooc_stats` | Out-of-control points, failures caught, precision, recall and lift per sensor |
| `feature_importance` | Random Forest sensor importances |
| `risk_quintiles_oot` | Failure rate by risk quintile on the out-of-time sample |
| `units_oot_scores` | Per-unit out-of-time risk scores |

## Citation

> McCann, M., & Johnston, A. (2008). *SECOM*. UCI Machine Learning Repository.
> https://doi.org/10.24432/C54305

Licensed CC BY 4.0. The data describes production units and anonymised sensor
readings only — no personal information.
