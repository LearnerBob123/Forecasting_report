# Data Preprocessing Log

## Day 2 Work

### Objective

Establish a clean and reliable preprocessing pipeline starting from raw 1-minute demand data and validate anomaly handling before model training.

### 1. Data Cleaning – Extreme Value Filtering

**Input:** Raw 1-minute demand data

**Process:** Removed unrealistic values outside defined physical bounds

**Range enforced:**
```
0 ≤ Demand ≤ 10,000
```

**Method:** Numeric coercion (`errors='coerce'`) and masking logic

**Outcome:** Eliminated negative values, corrupted entries, and unrealistic spikes

### 2. Resampling

**Transformation:** Converted frequency from 1-minute to 15-minute

**Method:** Mean aggregation
```python
.resample('15T').mean()
```

**Purpose:**
- Reduce high-frequency noise
- Align with forecasting resolution
- Stabilize short-term volatility

### 3. Timestamp Standardization

- Ensured consistent datetime formatting
- Handled sub-second precision artifacts (`.000000000`)
- Set Timestamp as primary index
- Enforced uniform format: `%Y-%m-%d %H:%M:%S`

**Saved cleaned dataset as:** `master_1min_clean.csv`

### 4. Column Selection

**Removed auxiliary demand components:**
- Vedanta demand
- Solar demand
- Other non-target variables

**Retained:** Primary demand column

**Goal:** Maintain a minimal and modeling-focused dataset

### 5. Statistical Outlier Detection & Evaluation

**Initial Implementation**

Three statistical methods were implemented:
- IQR-based detection
- Z-score detection
- Moving-average multiplier detection

**Observations**

After overlap analysis:
- IQR added no unique anomalies
- Z-score added minimal additional detections
- Moving-average method captured the vast majority of meaningful anomalies

**Conclusion:** Global distribution methods (IQR, Z-score) were not significantly relevant for this seasonal demand dataset

### 6. Moving Average–Based Outlier Removal

After extensive parameter testing:
- Window size experimentation
- Multiplier tuning
- Sensitivity evaluation

**Final configuration was selected based on:**
- Logical anomaly capture
- Preservation of seasonal structure
- Minimal distortion of natural valleys and peaks

Outliers were removed and replaced using a season-aware filling strategy (week-shift).

### 7. Visual Validation After Cleaning

**Post-cleaning validation included:**
- Daily curve inspection
- Monthly pattern comparison
- Valley structure verification
- Checking for artificial flatlines
- Ensuring no logical discontinuities were introduced

**Result:** The cleaned dataset maintains:
- Seasonal integrity
- Structural consistency
- Logical valley behavior
- No abnormal distortion

## Status: Preprocessing Phase Completed

The primary demand dataset is now cleaned, validated, and structurally sound.

## Next Phase

### 1. Weather–Demand Dataset Creation

**Tasks:**
- Identify relevant weather parameters: Temperature, Humidity, Wind speed, Others (to be finalized)
- Decide: Region-to-demand zone mapping and weather station alignment strategy
- Create merged weather–demand file at 15-minute resolution

### 2. Statistical Analysis

**Perform exploratory analysis:**
- Correlation between demand and temperature
- Seasonal dependency checks
- Regional variability comparison
- Lag analysis
- Preliminary feature importance exploration
