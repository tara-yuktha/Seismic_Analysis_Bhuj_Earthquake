# Code Description — Bhuj Earthquake 2001 Notebook

This document provides a detailed description of every code cell in `Bhuj_Earthquake.ipynb`, explaining what each block does, why it is used, and what output it produces.

---

## Cell 1 — Event Catalog Retrieval

```python
from obspy import UTCDateTime
from obspy.clients.fdsn import Client

start_time = UTCDateTime("2001-01-26T00:00:00.000")
end_time = UTCDateTime("2001-01-28T00:00:00.000")
...
client = Client("IRIS")
catalog = client.get_events(...)
```

**What it does:**
Connects to the IRIS (Incorporated Research Institutions for Seismology) FDSN web service and retrieves all recorded seismic events in the Bhuj region between 26–28 January 2001.

**Key parameters:**
- `starttime` / `endtime` — 48-hour window around the mainshock
- `minlatitude`, `maxlatitude`, `minlongitude`, `maxlongitude` — geographic bounding box covering the Kutch region (23.42°N–25.00°N, 70.23°E–77.00°E)
- `maxdepth=1000` — includes all shallow to deep-focus events
- `minmagnitude=0`, `maxmagnitude=8` — captures all events including foreshocks and aftershocks
- `orderby="time-asc"` — sorts events chronologically

**Output:** An ObsPy `Catalog` object containing all matching seismic events.

---

## Cell 2 — Catalog Parsing, DataFrame Creation & Magnitude Histogram

```python
import matplotlib.pyplot as plt
import pandas as pd
from IPython.display import display

for event in catalog:
    dates.append(...)
    ...

earthquake_data = pd.DataFrame({...})
earthquake_data = earthquake_data.sort_values(by=["time"], ascending=True)
display(earthquake_data)

plt.hist(earthquake_data['magnitude'], bins=10, ...)
plt.show()
```

**What it does:**
Iterates through each event in the ObsPy catalog and extracts 7 attributes — date, time, latitude, longitude, depth, magnitude, and magnitude type — into separate lists. These are then assembled into a Pandas DataFrame for easy manipulation.

The DataFrame is sorted by time of day and displayed as an interactive table in the notebook.

A **magnitude distribution histogram** is then plotted to visualise how many events occurred at each magnitude level, giving an overview of the seismic activity spread.

**Output:**
- An interactive table showing all seismic events
- A histogram of earthquake magnitudes

---

## Cell 3 — Export Catalog to CSV

```python
import os
save_path = "Earthquake_Catalog"
if not os.path.exists(save_path):
    os.makedirs(save_path)
earthquake_data.to_csv(save_path + "/catalog.csv", index=True)
```

**What it does:**
Creates a folder called `Earthquake_Catalog/` (if it doesn't already exist) and saves the earthquake DataFrame to a CSV file at `Earthquake_Catalog/catalog.csv`. This allows the data to be reloaded in later cells without re-querying the IRIS server.

**Output:** `Earthquake_Catalog/catalog.csv`

---

## Cell 4 — Spatial & Depth Visualisations + Interactive 3D Plot

```python
import plotly.express as px
data = pd.read_csv("Earthquake_Catalog/catalog.csv")

# Spatial Distribution (Epicenter Map)
plt.scatter(data["longitude"], data["latitude"], c=data["magnitude"], ...)

# Longitude-Depth Plot
plt.scatter(data["longitude"], data["depth"], ...)

# Latitude-Depth Plot
plt.scatter(data["latitude"], data["depth"], ...)

# Interactive 3D Scatter Plot
fig = px.scatter_3d(data, x="latitude", y="longitude", z="depth", color="magnitude", ...)
fig.show()
```

**What it does:**
Loads the saved CSV and produces four visualisations:

1. **Epicenter Map** — a 2D scatter plot of event locations (longitude vs latitude) coloured and sized by magnitude. Larger, brighter dots indicate stronger events.

2. **Longitude-Depth Plot** — shows how earthquake depth varies along the east-west axis. The y-axis is inverted so deeper events appear lower on the plot, matching the physical reality underground.

3. **Latitude-Depth Plot** — same concept along the north-south axis, revealing the dip direction of the fault zone.

4. **Interactive 3D Scatter Plot** — a Plotly-based interactive chart where the user can rotate, zoom, and hover to explore the 3D distribution of events by latitude, longitude, and depth. Colour and point size both encode magnitude.

**Output:** 3 static Matplotlib plots + 1 interactive Plotly chart

---

## Cell 5 — Depth Distribution & Magnitude-Depth Plot

```python
# Depth Distribution Histogram
plt.hist(data["depth"], bins=20, color="Green", alpha=0.7)

# Magnitude-Depth Scatter Plot
plt.scatter(data["magnitude"], data["depth"], c="purple", alpha=0.7)
```

**What it does:**
Two additional plots that focus specifically on the depth dimension:

1. **Depth Distribution Histogram** — shows the frequency of events at different focal depths, revealing whether most activity is shallow (crustal) or deeper.

2. **Magnitude-Depth Scatter** — examines whether larger earthquakes tend to occur at specific depths, which can hint at fault geometry and stress distribution.

**Output:** 2 static Matplotlib plots

---

## Cell 6 — Mainshock Event Extraction

```python
from datetime import datetime
from obspy import UTCDateTime

event_index = 3
event_data = earthquake_data.loc[event_index, :]

eq_lat = event_data["latitude"]
eq_lon = event_data["longitude"]
eq_depth = event_data["depth"]
eq_mag = event_data["magnitude"]
eq_mag_type = event_data["magnitude_type"]
eq_dt = UTCDateTime(datetime.combine(event_data["date"], event_data["time"]))

print(f"Date of the event: {eq_dt}")
...
```

**What it does:**
Selects the mainshock event (index 3 in the sorted catalog, which corresponds to the highest-magnitude event — the Mw 7.7 Bhuj mainshock) and extracts its individual attributes into named variables. These variables (`eq_lat`, `eq_lon`, `eq_depth`, etc.) are reused in subsequent mapping and waveform cells.

**Output:** Printed summary of the mainshock — date, latitude, longitude, depth, magnitude, and magnitude type.

---

## Cell 7 — Epicenter Map (Close-up Basemap)

```python
from mpl_toolkits.basemap import Basemap

m = Basemap(projection='merc', ...)
m.drawcoastlines()
m.drawcountries()
m.drawstates()
m.fillcontinents(color='white', lake_color='#46bcec')

event_lons, event_lats = m(event_lon, event_lat)
m.scatter(event_lons, event_lats, marker='*', color='r', zorder=5)
plt.show()
```

**What it does:**
Creates a close-up geographic map of the Bhuj region using the Mercator projection. The map renders coastlines, country borders, state boundaries, and fills oceans in blue. The mainshock epicenter is plotted as a red star marker labelled with its magnitude (`jan(7.7)`).

This cell gives a geographically accurate view of where the earthquake occurred within the Kutch region of Gujarat.

**Output:** A regional Basemap plot showing the earthquake epicenter.

---

## Cell 8 — Station Location Map (Wide-area Basemap with ETOPO)

```python
m = Basemap(projection='mill', ...)
m.etopo()
m.scatter(event_lons, event_lats, marker='*', color='yellow', ...)   # Epicenter
m.scatter(lons, lats, marker='v', color='red', ...)                  # Stations
plt.savefig('plot1.png', dpi=300)
plt.show()
```

**What it does:**
Creates a wide-area map spanning South Asia and Central Asia to show the spatial relationship between the earthquake epicenter and all 5 seismic stations used in the analysis (NIL, ABKT, PALT, AAK, LSA).

- The background uses **ETOPO** global relief data (topography + bathymetry) for a realistic terrain view
- The epicenter is marked with a **yellow star**
- Stations are marked with **red triangles**
- The map boundaries are auto-calculated from station coordinates plus a 15° margin
- Latitude/longitude gridlines are drawn at 5° intervals
- The final map is saved as `plot1.png` at 300 DPI

**Output:** A high-resolution wide-area station map saved as `plot1.png`

---

## Cell 9 — Station Metadata Retrieval

```python
client = Client("IRIS")
station1 = client.get_stations(
    network="II", station="NIL", channel="BH?",
    level="channel", starttime=startt, endtime=endtime
)
print(station1)
```

**What it does:**
Queries the IRIS FDSN service for metadata about the NIL station (Nilore, Pakistan) on Network II. The `BH?` wildcard retrieves all broadband channels (BHZ, BHN, BHE). The `level="channel"` parameter returns detailed channel-level information including sample rate, instrument response, and orientation.

This metadata is important for understanding what data is available before downloading waveforms.

**Output:** Printed station metadata for II.NIL including channel details.

---

## Cell 10 — Single Station Waveform Plot (NIL)

```python
st = client.get_waveforms("II", "NIL", "*", "BHZ",
                           UTCDateTime("2001-01-26T00:00:00"),
                           UTCDateTime("2001-01-27T00:00:00"))
tr = st[0]
fig = tr.plot()
```

**What it does:**
Downloads the full 24-hour vertical-component (BHZ) broadband waveform from the NIL station on the day of the earthquake. The first trace is extracted and plotted using ObsPy's built-in `.plot()` method, which shows the seismogram as a time-series (amplitude vs. time).

The P-wave arrival from the Bhuj mainshock is visible at approximately `2001-01-26T03:18:00` UTC.

**Output:** A time-series seismogram plot for II.NIL.BHZ

---

## Cell 11 — Multi-station Waveform Download & MiniSEED Export

```python
start_time = UTCDateTime("2001-01-26T03:16:40")
end_time = start_time + 100 * 60   # 100 minutes post-event

stations_networks = [("NIL","II"), ("ABKT","II"), ("PALK","II"), ...]

for station, network in stations_networks:
    st = client.get_waveforms(network, station, "*", "BH*", ...)
    all_waveforms += st
    for trace in st:
        trace.write(filepath, format="MSEED")
```

**What it does:**
Loops through all 10 station-network pairs and downloads BH* (all broadband) waveforms starting from the mainshock origin time (`03:16:40 UTC`) for 100 minutes. This window captures the P-wave, S-wave, and early surface wave arrivals.

Each trace is saved individually as a MiniSEED file (`.mseed`) in the `miniSEED/` directory, named with the format `NETWORK_STATION_LOCATION_STARTTIME_ENDTIME_CHANNEL.mseed`. Stations that return no data (due to data gaps or distance) are skipped with an informative message.

**Output:**
- MiniSEED files saved to `miniSEED/` directory
- Console messages confirming each saved file or reporting errors

---

## Cell 12 — Load, Merge & Plot All Waveforms

```python
for filename in os.listdir(mseed_dir):
    stream = read(filepath)
    all_waveforms += stream

valid_waveforms.traces = [trace for trace in valid_waveforms if trace.stats.npts > 0]
valid_waveforms.merge(method=1, fill_value='interpolate')
valid_waveforms.plot(type="trace", show=True)
```

**What it does:**
Reads all `.mseed` files from the `miniSEED/` directory back into memory. Traces with zero samples (empty records from stations that returned no data) are filtered out. The remaining traces are merged using interpolation to fill any small gaps, then plotted together in a single multi-panel figure — one panel per trace.

**Output:** A multi-panel seismogram figure showing all valid waveforms across stations.

---

## Cell 13 — Combined Amplitude Spectrum (Multiple Stations)

```python
for filename in station_files:
    stream = read(filepath)
    trace = stream[0]
    trace.detrend(type="demean")
    trace.taper(max_percentage=0.05)

    fft_vals = np.fft.rfft(data)
    freqs = np.fft.rfftfreq(npts, d=1.0/sampling_rate)
    amplitude_spectrum = np.abs(fft_vals)

    plt.plot(freqs, amplitude_spectrum, label=f'Station: {trace.stats.station}')

plt.xscale('log')
plt.yscale('log')
plt.show()
```

**What it does:**
For each of the 5 selected station files (NIL, ABKT, PALK, AAK, LSA), the trace is pre-processed and then transformed from the time domain to the frequency domain using a **Fast Fourier Transform (FFT)**:

1. **Demean** — removes the DC offset (mean value) from the signal
2. **Taper** — applies a 5% cosine taper to both ends of the signal to reduce spectral leakage
3. **rfft** — computes the real FFT and returns complex coefficients
4. **Amplitude spectrum** — computed as the absolute value of the FFT coefficients (`|FFT|`)

All station spectra are overlaid on a single **log-log plot**, making it easy to compare amplitudes across stations. NIL's spectrum sits significantly higher (~10¹⁰), confirming it is closest to the epicenter.

**Output:** A combined log-log amplitude spectrum plot for all stations.

---

## Cell 14 — Per-Station Amplitude Spectrum

```python
for tr in st:
    D = np.fft.rfft(tr.data)
    freqs = np.fft.rfftfreq(tr.stats.npts, d=tr.stats.delta)
    plt.plot(freqs, np.abs(D), color=(0, 0, 1), linewidth=0.5)
    plt.xscale("log")
    plt.xlim(0.01, 5)
    plt.title(f"Amplitude Spectrum (Raw Data) - {network}.{station}..{channel}")
    plt.show()
```

**What it does:**
Similar to Cell 13 but generates a **separate amplitude spectrum plot for every individual trace** across the selected stations (II_NIL, II_AAK, IC_LSA, II_PALK). The frequency axis is limited to 0.01–5 Hz, the most seismically relevant range for this event. Each plot is titled with the full station/channel identifier.

This allows close inspection of the frequency content at each individual station and channel, helping identify which channels captured the clearest signal.

**Output:** Individual amplitude spectrum plots per trace (one plot per channel per station).

---

## Cell 15 — Signal Filtering & Spectrogram Generation

```python
st_bandpass = st_raw.copy().filter("bandpass", freqmin=0.1, freqmax=4.95)
st_lowpass  = st_raw.copy().filter("lowpass",  freq=0.5)
st_highpass = st_raw.copy().filter("highpass", freq=1.0)

for tr in st_bandpass:
    tr.spectrogram(log=True, title=f"Spectrogram for {tr.id}", clip=[0.0, 0.3])
    plt.show()
```

**What it does:**
Applies three standard seismic signal processing filters to the raw waveform data:

- **Bandpass (0.1–4.95 Hz)** — the most informative filter for this event; passes intermediate frequencies and suppresses both very low-frequency noise and high-frequency noise
- **Lowpass (≤ 0.5 Hz)** — isolates long-period energy, useful for identifying surface waves (Love and Rayleigh waves)
- **Highpass (≥ 1.0 Hz)** — isolates short-period energy, useful for highlighting body waves (P and S waves)

A **spectrogram** is then generated for each trace from the bandpass-filtered stream. The spectrogram shows how the frequency content of the signal evolves over time — x-axis is time, y-axis is frequency (log scale), and colour intensity represents energy amplitude. The `clip=[0.0, 0.3]` parameter controls the colour scale to enhance visibility of weaker signals.

**Output:** Spectrogram plots for each trace, showing time-frequency energy distribution.

---

## Summary Table

| Cell | Purpose | Key Libraries | Output |
|------|---------|---------------|--------|
| 1 | Fetch event catalog from IRIS | `obspy` | `Catalog` object |
| 2 | Parse catalog → DataFrame + magnitude histogram | `pandas`, `matplotlib` | Table + histogram |
| 3 | Export catalog to CSV | `os`, `pandas` | `catalog.csv` |
| 4 | Spatial, depth, and 3D visualisations | `matplotlib`, `plotly` | 4 plots |
| 5 | Depth distribution and magnitude-depth plots | `matplotlib` | 2 plots |
| 6 | Extract mainshock attributes | `obspy`, `datetime` | Printed event info |
| 7 | Close-up epicenter map | `basemap`, `matplotlib` | Epicenter map |
| 8 | Wide-area station + epicenter map | `basemap`, `numpy` | `plot1.png` |
| 9 | Query NIL station metadata | `obspy` | Printed metadata |
| 10 | Plot 24-hr waveform at NIL | `obspy` | Seismogram plot |
| 11 | Download + save waveforms from 10 stations | `obspy`, `os` | `.mseed` files |
| 12 | Load, merge, and plot all waveforms | `obspy`, `matplotlib` | Multi-trace plot |
| 13 | Combined amplitude spectrum (5 stations) | `numpy`, `matplotlib` | Log-log spectrum |
| 14 | Per-station amplitude spectrum | `numpy`, `matplotlib` | Individual spectra |
| 15 | Apply filters + generate spectrograms | `obspy`, `matplotlib` | Spectrogram plots |
