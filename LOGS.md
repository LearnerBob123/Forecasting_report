# Data Preprocessing Log: Demand Forecasting Project

## Day 1 Work

### Objective
Establish a clean and reliable preprocessing pipeline starting from raw 1-minute demand data.

---

### 1. Data Cleaning – Extreme Value Filtering
* **Input:** Raw 1-minute demand data.
* **Process:** Removed unrealistic values outside the defined bounds:
    * **Range:** $0 \leq \text{Demand} \leq 10,000$
* **Outcome:** Corrupted or erroneous spikes and negative values were eliminated using `errors='coerce'` and masking logic.

### 2. Resampling
* **Transformation:** Converted data frequency from **1-minute** to **15-minute**.
* **Method:** Mean aggregation (`.resample('15T').mean()`).
* **Outcome:** Reduced high-frequency noise and aligned the dataset with the standard modeling frequency.

### 3. Timestamp Standardization
* **Formatting:** Verified proper datetime formatting and handled sub-second precision errors (`.000000000`).
* **Indexing:** Set `Timestamp` as the primary index and ensured a consistent datetime format (`%Y-%m-%d %H:%M:%S`).
* **Storage:** Saved the cleaned dataset to `master_1min_clean.csv`.

### 4. Column Selection
* **Action:** Dropped auxiliary and redundant variables to focus strictly on the target.
* **Removed:** Vedanta demand, Solar energy demand, and other auxiliary columns.
* **Retained:** Main demand column.
* **Purpose:** Maintain a minimal, modeling-focused dataset.

### 5. Outlier Inspection (Visual Validation)
* **Exploratory Data Analysis (EDA):**
    * Plotted daily demand curves to check for "flatlines" from filling.
    * Plotted monthly demand curves to identify seasonality.
* **Verification:** Confirmed seasonal patterns and structural consistency; ensured no abnormal distortions were introduced during the cleaning/filling process.

---

### Pending Tasks
- [ ] Implement statistical outlier removal (Z-score or IQR).
- [ ] Re-validate the cleaned dataset for continuity.
- [ ] Merge with external weather parameters.
- [ ] Begin exploratory correlation analysis (Demand vs. Temperature).