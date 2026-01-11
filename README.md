# Landsat H3 Change Detection

Detect vegetation changes over time using Landsat imagery aggregated to H3 hexagons.

Compares EVI/NDVI between two time periods (e.g., 1990s vs 2020s) and outputs change metrics per H3 cell.

## Background

**Case Study: Chernobyl Exclusion Zone**

The 1986 Chernobyl disaster was catastrophic—the worst nuclear accident in history. Yet in the decades since evacuation, the Exclusion Zone has become an unintentional experiment in what happens when humans withdraw from a landscape. This notebook uses satellite imagery to examine one aspect of that story: vegetation change between the early 1990s and 2020s.

Inspired by [Cal Flyn](https://www.calflyn.com/)'s *Islands of Abandonment*, which includes her trip to Chernobyl and Pripyat before the war.

## Setup

```bash
# Install uv if you haven't
curl -LsSf https://astral.sh/uv/install.sh | sh

# Clone and install
git clone https://github.com/kentstephen/landsat_diff_h3.git
cd landsat_diff_h3
uv sync
```

## Data Sources

This project supports two STAC endpoints for Landsat data:

### Option 1: Element84 Earth Search (default)

Free, no authentication required. Uses requester-pays S3 buckets.

```bash
# Create .env file with your AWS credentials
AWS_ACCESS_KEY_ID=your_key
AWS_SECRET_ACCESS_KEY=your_secret

# Optional: Mapbox token for satellite basemap with labels
MAPBOX_TOKEN=your_mapbox_token
```

### Option 2: Microsoft Planetary Computer

Free with registration. Better for users without AWS accounts.

1. Sign up at [planetarycomputer.microsoft.com](https://planetarycomputer.microsoft.com)
2. Get your API key
3. Update the notebook to use Planetary Computer's STAC endpoint

## Usage

Open `landsat_vegetation_change_h3.ipynb` in Jupyter or VS Code:

```bash
uv run jupyter lab
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
- [`quackosm`](https://github.com/kraina-ai/quackosm) - OpenStreetMap PBF to GeoDataFrame via DuckDB
- [`dask`](https://github.com/dask/dask) - Parallel computing with task scheduling
- [`pyarrow`](https://github.com/apache/arrow) - Apache Arrow for zero-copy data interchange
