# Landsat H3 Change Detection

Detect vegetation changes over time using Landsat imagery aggregated to H3 hexagons.

Compares EVI (Enhanced Vegetation Index) and NDVI (Normalized Difference Vegetation Index) between two time periods (e.g., 1990s vs 2020s) and outputs change metrics per H3 cell.

<img width="1244" height="533" alt="image" src="https://github.com/user-attachments/assets/68e72eff-9ebb-4b14-86cc-db841f99a54b" />

<img width="1250" height="601" alt="Screenshot 2026-01-10 at 3 31 59 PM" src="https://github.com/user-attachments/assets/8b232464-16a1-41f5-aecd-ab0f43e74ee1" />


## Background

**Case Study: Chernobyl Exclusion Zone**

The 1986 Chernobyl disaster was catastrophic—the worst nuclear accident in history. Yet in the decades since evacuation, the Exclusion Zone has become an unintentional experiment in what happens when humans withdraw from a landscape. This notebook uses satellite imagery to examine one aspect of that story: vegetation change between the early 1990s and 2020s.

Inspired by [Cal Flyn](https://www.calflyn.com/)'s *Islands of Abandonment*, which includes her trip to Chernobyl and Pripyat before the Russian invasion.

I think this type of tool could be useful for analysis of change over time and can be repurposed for different types of sensors and other spectral indices.

## Setup

[Astral Docs for `uv`](https://docs.astral.sh/uv/getting-started/installation/#__tabbed_1_1)

```bash
# Install uv if you haven't
curl -LsSf https://astral.sh/uv/install.sh | sh

# Or using homebrew with macOS
brew install uv

# Clone and install
git clone https://github.com/kentstephen/landsat_diff_h3.git
cd landsat_diff_h3
uv sync
```

## Data Sources

This project supports two STAC endpoints for Landsat data:

### Option 1: Element84 Earth Search

Uses requester-pays S3 buckets. You will need to use new or existing AWS secrets. For purposes of accessing Landsat imagery in S3 this way, the IAM user defined for this purpose will only need `AmazonS3ReadOnlyAccess`.

```bash
# Create .env file with your AWS credentials
AWS_ACCESS_KEY_ID=your_key
AWS_SECRET_ACCESS_KEY=your_secret

# Optional: Mapbox token for satellite basemap with labels
MAPBOX_TOKEN=your_mapbox_token
```

It cost me less than 20 cents of compute to develop this example using AWS/Element84.

### Option 2: Microsoft Planetary Computer

This notebook uses `sign_inplace` so no extra credentials are needed. However, AWS/Element84 is generally more reliable. 

## Usage

Open `landsat_vegetation_change_h3.ipynb` in Jupyter or your preferred IDE:

Example to run in Jupyter Lab: 
```bash
uvx juv run landsat_vegetation_change_h3.ipynb
```


## How It Works

1. Query STAC catalog for Landsat scenes in your AOI
2. Load imagery with odc-stac (uses dask for parallel S3 fetching)
3. Calculate EVI or NDVI per pixel
4. Aggregate to H3 hexagons using DuckDB's H3 extension
5. Compare early vs recent periods to compute change

## Core Dependencies

- [`odc-stac`](https://github.com/opendatacube/odc-stac) - Cloud-native raster loading
- [`duckdb`](https://github.com/duckdb/duckdb) + [H3 extension](https://github.com/isaacbrodsky/h3-duckdb) - Fast spatial aggregation
- [`h3`](https://github.com/uber/h3-py) - Uber's hexagonal grid system
- [`lonboard`](https://github.com/developmentseed/lonboard) - GPU-accelerated map visualization
- [`dask`](https://github.com/dask/dask) - Parallel array computing with task scheduling
- [`pyarrow`](https://github.com/apache/arrow) - Apache Arrow for zero-copy data interchange

*This notebook is not meant to be conclusive science - just a means of exploration*