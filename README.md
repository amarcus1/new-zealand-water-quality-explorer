# New Zealand Water Quality Viewer

🗺️ **[Live map](https://amarcus1.github.io/new-zealand-water-quality-explorer)** 

## The question

Where are regions with excellent and poor water quality in New Zealand? This project will first develop a viewer to visualize monitoring sites compiled by Land, Air, Water Aotearoa (LAWA). Analyses will be added periodically to dive deeper into the data.  

## The data

- **Water Quality Data.** Data sourced from LAWA (Land, Air, Water Aotearoa) and LAWA partner agencies, available under the Creative Commons Attribution 4.0 International License (CC BY 4.0). Links to [LAWA Website](https://www.lawa.org.nz) and [License](https://creativecommons.org/licenses/by/4.0).

## Data pipeline

- `01-data-pipeline.ipynb`: Cleans Excel data and combined by standardizing column names, ensuring consistent datatypes, removing duplicates, and dropping NaN.  Converts Excel to Parquet. 
- `02-creating-pmtile.ipynb`: Gathers summary statistics. Converts Parquet to PMTiles.

## Methodology

Built the same analysis twice:

- **Python (GeoPandas).** Equivalent pipeline in `analysis.ipynb`, with a static choropleth and interactive `.explore()` map. Final output exported to GeoParquet.

Both pipelines produce the same density values to within rounding. The Python version produces the visualization. The SQL version runs against a database at scale.

## Findings

- Top 5 Sites by Max Ammonium Concentration (mg-N/L):
  1. Moawhango at Waiouru  (50.6 mg/L)
  2. Piakatutu at d/s Sanson STP (38.7 mg/L)
  3. TTutaenui Stream at d/s Marton STP	(28.2 mg/L)
  4. WUnnamed Trib of Waipu at ds Ratana STP(23.4 mg/L)
  5. Newmarket Stream @ Ayr St (21.0 mg/L)

## How to run it

Requires Python 3.11+, [tippecanoe](https://github.com/felt/tippecanoe) (the Felt fork, which writes PMTiles directly), and Node.js (or any static server that supports HTTP range requests).

```bash
git clone https://github.com/{your-username}/{your-repo}.git
cd {your-repo}

# Python environment
python -m venv venv
source venv/bin/activate
pip install duckdb geopandas jupyterlab

# 1. Convert the LAWA Excel data to Parquet
jupyter lab 01-data-pipeline.ipynb

# 2. Summarize with DuckDB, export GeoJSON, build ammonium.pmtiles
jupyter lab 02-creating-pmtile.ipynb

# 3. Serve the map (a plain `python -m http.server` does not support range requests)
python -m RangeHTTPServer 8080
```

Then open `http://localhost:8080` in a browser. `index.html` and `ammonium.pmtiles` need to sit in the same folder.

## What I learned

- A LawaSiteID has multiple indicators, turbidity is measured in two ways (FTU and NTU), E. coli is measured in two ways (MPN and CFU) and some indicators can have duplicate measurements for the same collection date-time.

## Stack

- GeoPandas 
- DuckDB + GeoParquet
- Tippecanoe + PMTiles + MapLibre

## What's Next
- Maximum NH4 is over 20+ years. Analyaze the trends in indicator to identify sites where indicators are increasing or decreasing.
- Group indicators into categories (e.g., high, medium, and low ammonium) and color circles accordingly.  Add legend.
- Explore other indicators (e.g., turbidity, E. coli).