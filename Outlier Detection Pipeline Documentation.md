# Outlier Detection & Cleaning Pipeline

## Time-Series Demand Data (15-Minute Resolution)

## 1. Overview

This pipeline provides a structured and reproducible workflow for outlier handling in 15-minute demand time series.

The process is explicitly split into:

- Detection
- Visualization
- Cleaning/imputation
- Final validation

This avoids silent parameter drift and keeps cleaning deterministic.

---

## 2. Detection Methods

Three methods are supported.

### 2.1 IQR Method

Global distribution-based detection.

$$
IQR = Q_3 - Q_1
$$

$$
\text{Lower} = Q_1 - (k \times IQR), \quad \text{Upper} = Q_3 + (k \times IQR)
$$

- Parameter: `iqr_k` (default `1.5`)
- Useful for broad global outliers in stable distributions

### 2.2 Z-Score Method

Standard deviation-based detection.

$$
z = \frac{x - \mu}{\sigma}
$$

$$
|z| > \text{threshold}
$$

- Parameter: `z_threshold` (default `3.0`)
- Useful for approximately normal global anomalies

### 2.3 Moving Average Multiplier Method

Local, time-aware detection around rolling mean.

$$
\text{band} = (\text{ma\_multiplier} - 1) \times |\text{MA}|
$$

$$
\text{Lower} = \text{MA} - \text{band}, \quad \text{Upper} = \text{MA} + \text{band}
$$

- Parameters: `ma_window`, `ma_multiplier`, optional `ma_min_periods`
- Best for seasonal/load time-series with local spikes

---

## 3. Zone-Wise Configuration (New)

The notebook now supports a per-zone configuration dictionary:

- One config block per demand column (zone)
- Each zone can use different methods and thresholds
- All zones can be processed in one run

Typical per-zone keys:

- `methods`: tuple like `("moving_average", "zscore")`
- `combine`: `"any"` or `"all"`
- `ma_window`, `ma_multiplier`, `ma_min_periods`
- `iqr_k`, `z_threshold`
- `remove_outliers`
- `fill_method`: `"ffill"`, `"bfill"`, `"week_shift"`, `"none"`
- `week_periods`

---

## 4. One-Shot Multi-Zone Pipeline

A dedicated runner function applies all zone configs at once:

- Function: `run_zone_config_pipeline(df, ZONE_CONFIG)`
- Outputs:
  - `cleaned_df`
  - `cleaned_mask_df`
  - `cleaned_summary`

This ensures every zone is cleaned using its own rules in one deterministic pass.

---

## 5. Visualization Workflow

### 5.1 Method-Level Outlier Visualization

Per-zone visualization includes:

- Original series
- Method-specific markers with different colors:
  - IQR (red)
  - Z-score (orange)
  - Moving Average (green)
- Moving Average envelope:
  - MA center line
  - MA lower bound
  - MA upper bound

### 5.2 Interactive Plotly View

Interactive per-zone charts provide:

- Zoom/pan
- Hover inspection
- Legend toggling of methods and envelope

---

## 6. Cleaning & Imputation

Cleaning uses detected masks and zone-specific rules.

Supported fill options:

1. `ffill`
2. `bfill`
3. `week_shift` (same 15-minute block one week before: $96 \times 7$)
4. `none`

---

## 7. Final Validation (Post-Cleaning)

The final notebook section now plots full data for each zone:

- Full cleaned series (Matplotlib)
- Original vs cleaned overlay (Matplotlib)
- Optional cleaned interactive Plotly charts

This is the final QA step before exporting or modeling.

---

## 8. Recommended Usage

1. Set per-zone rules in `ZONE_CONFIG`
2. Run one-shot pipeline
3. Review method-level and envelope plots
4. Validate original vs cleaned overlays
5. Freeze configuration for reproducibility

Core principle: always visualize before and after cleaning, and keep mask/summary logs for traceability.
