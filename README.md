# 🧠 EEG Signal Preprocessing Pipeline

This repository contains a robust, reproducible, and standardized **EEG (Electroencephalography)** signal preprocessing pipeline built using **MNE-Python**. The goal of this phase is to remove environmental and physiological artifacts, filter out noise, and re-reference the data to prepare it for frequency domain analysis (e.g., PSD) and machine learning applications.

---

## 🖼️ Visualizations & Signal Comparisons

The repository includes visual proof and diagnostic plots comparing the signal across key preprocessing stages:

1. **Before & After Bandpass Filtering:** Highlights the removal of low-frequency baseline drifts and high-frequency muscular noise.
2. **After ICA Cleaning:** Demonstrates the successful removal of eye-blink artifacts (specifically component `ICA002`) while preserving natural cortical activity across channels.

*(Check the repository's root directory or figures folder to view these output plots).*

---

## 🛠️ Preprocessing Steps (Detailed Pipeline)

### 1. Spatial Montage & Channel Mapping 📍
* **Channel Renaming:** Standardized channel labels according to the international **10-20 System**.
* **Montage Assignment:** Applied the `standard_1020` spatial coordinate system to enable precise 2D topographic mapping (Topomaps) and accurate spatial analysis.

### 2. Frequency Filtering (Bandpass) 📶
* **Bandpass Filter Settings:** Applied a **2 Hz to 30 Hz** zero-phase FIR filter.
  * **High-pass (2 Hz):** Effectively removes low-frequency drifts, baseline shifts, and motion artifacts caused by slow body or head movements.
  * **Low-pass (30 Hz):** Suppresses high-frequency muscular noise (EMG) and power line interference.
* 🔌 **Note on Power Line Noise (Notch Filter):** The hardware recording device had built-in hardware filters to eliminate 50 Hz/60 Hz power line interference. Furthermore, since our software bandpass filter cuts off frequencies above 30 Hz, applying an additional 50 Hz Notch filter was unnecessary.

### 3. Artifact Removal via Independent Component Analysis (ICA) 👁️
* **Decomposition:** Decomposed the 19-channel continuous EEG data into **15 Independent Components (ICA000 to ICA014)**.
* **Ocular Artifact Identification:** Evaluated the spatial topomaps and time-series representations of all components. Component **`ICA002`** was identified as the primary ocular artifact (blink/vertical eye movement) due to its heavy energy concentration strictly localized in the frontal area ($F_{p1}$ / $F_{p2}$).
* **Signal Reconstruction:** Excluded `ICA002` (`ica.exclude = [2]`) and applied the ICA transformation back to the raw data (`raw_cleaned`), successfully removing eye blinks while preserving underlying cortical activity.

### 4. Average Re-referencing ⚖️
* **Re-referencing Scheme:** Converted the original recording reference to an **Average Reference** (`ref_channels='average'`).
* **Purpose:** Subtracting the instantaneous mean voltage of all electrodes reduces the impact of noise localized at the initial reference site (e.g., earlobes) and provides an unbiased baseline across the scalp surface.

---

## 📊 Summary of Data Transformation

| Stage | Action Applied | Primary Purpose |
| :--- | :--- | :--- |
| **Setup** | Standard 10-20 Montage | Map 3D sensor positions onto scalp geometry |
| **Filtering** | Bandpass (2 - 30 Hz) | Remove baseline drift (<2 Hz) and high-frequency noise (>30 Hz) |
| **Artifact Removal** | Exclude `ICA002` | Eliminate eye blink spikes from frontal channels ($F_{p1}$, $F_{p2}$) |
| **Re-referencing** | Common Average Reference | Unbias electrode voltages relative to the entire scalp mean |

---

## 🚀 Dependencies & Installation

To run the preprocessing scripts locally, ensure you have Python installed along with the required dependencies:

```bash
pip install mne matplotlib numpy
