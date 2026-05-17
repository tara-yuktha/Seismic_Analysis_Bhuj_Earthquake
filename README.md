# Bhuj Earthquake 2001 — Seismic Data Analysis

A comprehensive Python-based seismic analysis of the **2001 Bhuj earthquake (Mw 7.6/7.7)** that struck Gujarat, India on 26 January 2001 at 03:16:40 UTC. The earthquake killed approximately 20,000 people, injured ~1,67,000, and damaged over 3,40,000 buildings — making it one of the most devastating earthquakes in India's recorded history and among the deadliest intraplate earthquakes globally.

---

## Table of Contents

- [Background](#background)
- [Objectives](#objectives)
- [Features](#features)
- [Analysis Workflow](#analysis-workflow)
- [Visualisations](#visualisations)
- [Seismic Stations](#seismic-stations)
- [Key Findings](#key-findings)
- [Requirements](#requirements)
- [Installation](#installation)
- [Project Structure](#project-structure)
- [Usage](#usage)
- [Data Sources](#data-sources)
- [References](#references)

---

## Background

The Bhuj earthquake is geologically significant because it occurred within a **Stable Continental Region (SCR)** — an area not typically associated with major seismic activity. The epicenter was located in the Kutch region of Gujarat, which lies within a Mesozoic rift system. Faults in such rift systems are known to be capable of generating great earthquakes, as evidenced by a similar Mw 7.5 event in the same region in 1819.

The earthquake involved **reverse faulting** within the largely undeformed Indian lithosphere, and is believed to be a response to compressional forces from the ongoing India-Asia collision. GPS data indicates approximately 2 mm/yr of internal shortening in this region. The event provides a rare opportunity to study the rheology of the Indian lithosphere and understand intraplate seismicity in stable continental interiors.

---

## Objectives

1. Retrieve and catalogue seismic events (foreshocks, mainshock, and aftershocks) around the Bhuj region using the IRIS FDSN client
2. Analyse the spatial and temporal distribution of seismic activity
3. Extract and process seismic waveforms from multiple international stations
4. Compute and interpret amplitude spectra and spectrograms
5. Apply signal processing filters to isolate seismic wave phases
6. Visualise the earthquake source and surrounding station network

---

## Features

### Event Catalog Retrieval
- Queries the IRIS FDSN client for all seismic events in the Bhuj region between 26–28 January 2001
- Filters by geographic bounding box (lat: 23.42°N–25.00°N, lon: 70.23°E–77.00°E)
- Extracts date, time, latitude, longitude, depth, magnitude, and magnitude type for each event
- Exports the full catalog to CSV for further analysis

### Data Visualisation
- **Magnitude Distribution Histogram** — shows the frequency of events across magnitude bins
- **Epicenter Map (Spatial Distribution)** — scatter plot of event locations colour-coded by magnitude
- **Longitude-Depth Plot** — cross-sectional view of seismicity depth along the east-west axis
- **Latitude-Depth Plot** — cross-sectional view of seismicity depth along the north-south axis
- **Interactive 3D Scatter Plot** — Plotly-based 3D visualisation of latitude, longitude, and depth coloured by magnitude
- **Depth Distribution Histogram** — frequency distribution of earthquake focal depths
- **Magnitude-Depth Scatter Plot** — relationship between event depth and magnitude

### Mapping
- **Epicenter Map** using `mpl_toolkits.basemap` with coastlines, country and state boundaries
- **Regional Station Map** with ETOPO topography, showing the epicenter (yellow star) and all seismic stations (red triangles) used in the analysis

### Waveform Analysis
- Downloads BH-channel waveforms for 100 minutes post-event from 10 stations via IRIS
- Saves each trace as a MiniSEED (`.mseed`) file for offline reuse
- Merges and plots all valid waveforms in a single multi-trace figure
- Handles missing or zero-sample traces gracefully

### Amplitude Spectrum
- Computes FFT-based amplitude spectra for each station
- Plots all spectra on a shared log-log axis for direct comparison
- Per-station amplitude spectrum plots with log-frequency axis (0.01–5 Hz range)

### Spectrogram Analysis
- Applies three filters to the waveform data:
  - **Bandpass** (0.1–4.95 Hz) — isolates intermediate seismic signals
  - **Lowpass** (≤ 0.5 Hz) — highlights long-period surface waves
  - **Highpass** (≥ 1.0 Hz) — highlights short-period body waves
- Generates spectrograms (log scale) for each trace, revealing temporal frequency content

---

## Analysis Workflow

```
1. Define time window and bounding box
         ↓
2. Retrieve event catalog from IRIS FDSN
         ↓
3. Visualise spatial & statistical distribution
         ↓
4. Identify mainshock event (index 3 — highest magnitude)
         ↓
5. Map epicenter and nearby stations (Basemap)
         ↓
6. Download BH waveforms from 10 stations
         ↓
7. Save to MiniSEED, merge, and plot waveforms
         ↓
8. Compute FFT amplitude spectra per station
         ↓
9. Apply bandpass/lowpass/highpass filters
         ↓
10. Generate and interpret spectrograms
```

---

## Visualisations

| Plot | Description |
|------|-------------|
| Magnitude Histogram | Distribution of all events by magnitude |
| Epicenter Map | Spatial scatter of events coloured by magnitude |
| Lon/Lat-Depth Plots | Cross-sections showing depth variation |
| 3D Scatter Plot | Interactive 3D view of the seismic cloud |
| Basemap — Epicenter | Regional close-up map with the mainshock location |
| Basemap — Stations | Wide-area map with all seismic stations and epicenter |
| Waveform Plots | Time-series seismograms per station |
| Amplitude Spectra | FFT-based frequency content per station |
| Spectrograms | Time-frequency representation with applied filters |

---

## Seismic Stations

| Station | Network | Location | Notes |
|---------|---------|----------|-------|
| NIL | II | Nilore, Pakistan | Nearest station; highest amplitude (~10¹⁰) |
| ABKT | II | Alibek, Turkmenistan | Central Asia reference |
| PALK | II | Pallekele, Sri Lanka | Southern reference |
| AAK | II | Ala-Archa, Kyrgyzstan | Northern reference |
| LSA | IC | Lhasa, Tibet | Northeastern reference |
| RAYN | II | Ar Rayn, Saudi Arabia | Western reference |
| WMQ | IC | Ürümqi, China | Eastern reference |
| MAKZ | IU | Makanchi, Kazakhstan | Northern reference |
| CHTO | IU | Chiang Mai, Thailand | Southeastern reference |
| KURK | II | Kurchatov, Kazakhstan | Northern reference |

---

## Key Findings

- **NIL (Nilore, Pakistan)** recorded the highest amplitude (~10¹⁰ units), confirming it as the closest station to the epicenter among those selected
- **P-wave arrival** at II.NIL.10.BHZ was observed at approximately `2001-01-26T03:18:00` UTC — roughly 80 seconds after the event origin time
- Bandpass-filtered spectrograms (0.1–4.95 Hz) clearly reveal **body wave and surface wave phases**, with energy concentrated in the low-frequency range
- Amplitude in the II.NIL..BHE channel reached ~6.8 units (raw), further confirming proximity
- Several distant stations returned **no waveform data**, reflecting signal attenuation over distance
- The earthquake's occurrence in a stable continental region highlights the seismic hazard potential of ancient rift systems

---

## Requirements

- Python 3.8+
- Jupyter Notebook or JupyterLab

### Python Libraries

| Library | Purpose |
|---------|---------|
| `obspy` | Seismic data retrieval, waveform processing, spectrograms |
| `matplotlib` | Static plots and waveform figures |
| `pandas` | Event catalog management and CSV export |
| `numpy` | FFT computation and numerical operations |
| `plotly` | Interactive 3D scatter plots |
| `mpl_toolkits.basemap` | Geographic mapping |

---

## Installation

**1. Clone the repository:**
```bash
git clone https://github.com/<your-username>/Bhuj-Earthquake-Analysis.git
cd Bhuj-Earthquake-Analysis
```

**2. Install dependencies:**
```bash
pip install obspy matplotlib pandas numpy plotly
```

**3. Install Basemap (for mapping cells):**
```bash
pip install basemap
```

> **Note:** Basemap installation can be tricky on some systems. If `pip install basemap` fails, try:
> ```bash
> conda install -c conda-forge basemap basemap-data-hires
> ```
> See the [Basemap installation guide](https://matplotlib.org/basemap/stable/users/installation.html) for OS-specific instructions.

**4. Launch the notebook:**
```bash
jupyter notebook Bhuj_Earthquake.ipynb
```

---

## Project Structure

```
Bhuj-Earthquake-Analysis/
│
├── Bhuj_Earthquake.ipynb        # Main analysis notebook
│
├── Earthquake_Catalog/
│   └── catalog.csv              # Exported seismic event catalog
│
├── miniSEED/                    # Downloaded waveform files
│   ├── II_NIL_00_...BHZ.mseed
│   ├── II_ABKT_00_...BHE.mseed
│   └── ...                      # One .mseed file per trace per station
│
├── plot1.png                    # Station location map (saved output)
└── README.md
```

> **Note:** The `miniSEED/` and `Earthquake_Catalog/` directories are generated automatically when the notebook is run. They are not included in the repository.

---

## Usage

Run the notebook cells sequentially from top to bottom. Each section is self-contained:

- **Cells 1–3**: Event catalog retrieval and tabular display
- **Cells 4–6**: Statistical and spatial visualisations
- **Cell 7**: CSV export
- **Cells 8–9**: Event extraction and epicenter mapping
- **Cell 10**: Station location map
- **Cell 11**: Single-station waveform (NIL)
- **Cells 12–13**: Multi-station waveform download and plotting
- **Cells 14–15**: Amplitude spectra (combined and per-station)
- **Cell 16**: Filter application and spectrogram generation

> An active internet connection is required for cells that query the IRIS FDSN client.

---

## Data Sources

- **[IRIS FDSN](https://www.iris.edu/hq/)** — Seismic event catalog, station metadata, and BH-channel waveforms (Networks II, IC, IU)
- **[USGS Earthquake Hazards Program](https://www.usgs.gov/programs/earthquake-hazards)** — Regional earthquake severity maps and ShakeMap context

---

## References

1. Copley, A., Avouac, J. P., Hollingsworth, J., & Leprince, S. (2011). The 2001 Mw 7.6 Bhuj earthquake, low fault friction, and the crustal support of plate driving forces in India. [PDF](https://core.ac.uk/download/4890514.pdf)
2. Rajendran, C. P., Rajendran, K., Thakkar, M., & Goyal, B. (2008). Assessing the previous activity at the source zone of the 2001 Bhuj earthquake based on the near-source and distant paleoseismological indicators. *Journal of Geophysical Research*, 113(B5). https://doi.org/10.1029/2006jb004845
3. Mandal, P. et al. (2003). Characterization of the causative fault system for the 2001 Bhuj earthquake of Mw 7.7. *Tectonophysics*, 378(1–2), 105–121. https://doi.org/10.1016/j.tecto.2003.08.026
4. The Editors of Encyclopaedia Britannica. (2009). Bhuj earthquake of 2001. *Encyclopedia Britannica*. https://www.britannica.com/event/Bhuj-earthquake-of-2001
5. Paul, A. (2017). Bhuj Earthquake India 2001 – A complete study. *CivilDigital*. https://civildigital.com/bhuj-earthquake-india-2001-complete-study/
6. The 2001 Kutch (Bhuj) earthquake: Coseismic surface features and their significance. *JSTOR*. https://www.jstor.org/stable/24104957
