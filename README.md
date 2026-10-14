# Abuja Temperature Trend Analysis and Forecast

This project analyzes historical near-surface air temperature around Abuja, Nigeria, using ERA5 reanalysis data. It extracts the grid cell nearest to Abuja, summarizes monthly observations as annual means, estimates a linear historical trend, evaluates the trend on held-out years, and extrapolates annual mean temperatures for 2026–2035.

> **Forecast caveat:** The forecast is a simple statistical extrapolation of a linear trend. It is not a comprehensive climate projection and does not account for climate-model scenarios, changing variability, or other drivers of future temperature.

## Project contents

```text
.
├── analysis.py          # End-to-end analysis workflow
├── requirements.txt     # Python dependencies
├── data/
│   └── NIG-DATA.nc      # ERA5 NetCDF input dataset
└── output/              # Existing generated figures and forecast CSV
```

When run, `analysis.py` creates an `outputs/` directory (plural) and saves its generated files there. This differs from the existing `output/` directory in the repository, which contains previously generated artifacts.

## Requirements

- Python 3.10 or later
- The ERA5 NetCDF dataset at `data/NIG-DATA.nc`

Python packages and minimum versions are listed in `requirements.txt`: NumPy, pandas, xarray, netCDF4, SciPy, scikit-learn, and Matplotlib.

## Setup and run

From the project root, create and activate a virtual environment, install dependencies, and run the analysis:

```bash
python -m venv .venv
```

Activate the environment:

```bash
# Windows PowerShell
.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate
```

Install dependencies and run:

```bash
python -m pip install -r requirements.txt
python analysis.py
```

The script displays figures as well as saving them. In a headless environment, use a non-interactive Matplotlib backend (for example, set `MPLBACKEND=Agg`) if figure display is unavailable.

## Analysis workflow

1. Opens `data/NIG-DATA.nc` and reads the `t2m` variable.
2. Selects the nearest available grid point to 9.08°N, 7.40°E.
3. Converts temperature from Kelvin to degrees Celsius and keeps observations from 1980–2025.
4. Calculates annual means from years with all 12 monthly observations.
5. Fits a linear regression to the complete annual series and reports its slope, decadal trend, R², and linear-regression p-value.
6. Validates a separate linear model trained on 1980–2015 against observations from 2016 onward, reporting MAE and RMSE.
7. Fits a final model on all available complete historical years and predicts annual means for 2026–2035.
8. Exports annual data, validation results, forecasts, and figures.

## Generated outputs

Running the script writes these files under `outputs/`:

- `abuja_annual_temperature.csv` — annual observed means and fitted historical trend.
- `model_validation_results.csv` — held-out actual values, predictions, and errors.
- `abuja_temperature_forecast_2026_2035.csv` — forecast values by year.
- `historical_temperature_analysis.png` — monthly series and annual means with fitted trend.
- `abuja_temperature_forecast.png` — historical annual means and forecast.

The checked-in `output/` directory contains earlier artifacts and may not have the same filenames or columns as files produced by the current script.

## Configuration

The input path, coordinates, historical years, validation split, forecast period, temperature variable, and output directory are constants near the top of `analysis.py`. Update them there to adapt the workflow to another dataset, location, or period. The input dataset is expected to expose `latitude`, `longitude`, and `valid_time` coordinates and a `t2m` temperature variable.

## Methodological notes

- Annual averages include only years with 12 non-missing monthly observations.
- The validation period is a chronological holdout; it is not an independent climate projection evaluation.
- Linear extrapolation assumes the historical linear relationship continues through the forecast period. Treat the output as a trend-based estimate, not a year-specific prediction or an uncertainty-bounded projection.
- The exact grid coordinates selected depend on the resolution and coverage of the supplied ERA5 dataset.
