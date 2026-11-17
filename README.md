# EV Range & Demand Analysis

Business analytics project examining electric-vehicle registrations in Washington State. The project combines exploratory data analysis with machine-learning models to understand where observed EV demand is concentrated, which manufacturers and models lead the market, and which vehicle attributes are associated with electric range.

## Questions

- Which counties, manufacturers, and models have the greatest observed EV demand?
- How do electric range distributions differ between battery-electric vehicles (BEVs) and plug-in hybrids (PHEVs)?
- How has the registered EV population changed across model years?
- Can electric range be predicted from vehicle attributes?
- Which attributes contribute most to range predictions?

## Repository contents

| File | Description |
| --- | --- |
| [`BA_labca final.ipynb`](./BA_labca%20final.ipynb) | Complete data-cleaning, analysis, visualization, and modelling workflow |
| [`Electric Vehicle Population Data — 2026.csv`](./Electric%20Vehicle%20Population%20Data%20%E2%80%942026.csv) | Washington State DMV EV registration dataset |
| [`EV_Range_Demand_Analysis.pptx`](./EV_Range_Demand_Analysis.pptx) | Presentation of the analysis, results, business takeaways, and limitations |

## Dataset

The source dataset contains **289,564 registration records** and **16 original columns**. Each row represents a registered electric vehicle. Important fields include:

- Vehicle identity: make, model, model year, VIN prefix, and DOL vehicle ID
- Powertrain: EV type, electric range, and CAFV eligibility
- Geography: county, city, state, postal code, legislative district, vehicle location, and census tract
- Infrastructure context: electric utility

The data is from Washington State DMV registration records. Registration counts are used as a measure of **observed demand**, not as direct sales, revenue, or purchase-intent figures.

## Analysis workflow

The notebook:

1. Loads and inspects the CSV with pandas.
2. Standardizes column names and trims categorical text.
3. Removes exact duplicate records and corrects data types for geographic identifiers.
4. Investigates missing values instead of dropping records indiscriminately.
5. Treats applicable zero electric-range values as missing because the source uses zero when range has not been researched.
6. Summarizes observed demand by manufacturer, model, county, and range category.
7. Compares BEV and PHEV range distributions and examines range by model year.
8. Trains and evaluates Linear Regression and Random Forest Regressor pipelines.
9. Uses feature importance to identify the strongest predictors of electric range.

## Machine-learning approach

**Target:** `electric_range`

**Predictors used in the current notebook:**

- `model_year`
- `make`
- `model`
- `ev_type`
- `cafv_eligibility`

The notebook uses an 80/20 train-test split. Numeric values are median-imputed, categorical values are most-frequent-imputed and one-hot encoded, and the preprocessing is included inside each scikit-learn pipeline.

CAFV eligibility can incorporate range information, so it may introduce target leakage. It is currently included in the notebook’s feature list; remove it from `features` for a leakage-safe variant and re-evaluate the models before using the results operationally.

### Reported test-set results

| Model | R² | MAE (miles) | RMSE (miles) |
| --- | ---: | ---: | ---: |
| Linear Regression | 0.9548 | 13.5931 | 20.7022 |
| Random Forest | **0.9966** | **1.1696** | **5.6361** |

The Random Forest performed better on all three reported metrics. The notebook’s feature-importance analysis identifies EV type as the strongest predictor, followed by model year and vehicle-specific attributes.

## Key findings

- Observed EV demand is concentrated geographically, especially in King, Snohomish, and Pierce counties.
- Tesla leads the manufacturer-level registration counts in the analysed data; a relatively small group of manufacturers and models accounts for a large share of registrations.
- The range distribution is bimodal: PHEVs generally cluster at lower electric ranges, while BEVs generally have substantially higher ranges.
- Newer model years tend to show higher observed electric range.
- Vehicle attributes can predict electric range accurately in this dataset, with the Random Forest achieving an R² of approximately 0.9966 on the held-out test set.

## Run the notebook

### Requirements

- Python 3.9+
- Jupyter Notebook or JupyterLab
- pandas
- NumPy
- Matplotlib
- Seaborn
- scikit-learn

Install the dependencies with:

```bash
python -m pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

### Steps

1. Clone or download this repository.
2. Keep the notebook and CSV in the same directory.
3. Start Jupyter:

   ```bash
   jupyter notebook
   ```

4. Open [`BA_labca final.ipynb`](./BA_labca%20final.ipynb) and run the cells from top to bottom.

The CSV filename contains an em dash (`—`). If you rename the file, update the `pd.read_csv(...)` path in the notebook accordingly.

## Limitations

- The dataset covers Washington State, so findings may not generalize to other regions.
- Registration counts are a proxy for demand and do not represent sales, revenue, or future demand.
- The model does not include price, battery chemistry, charging infrastructure density, incentives, or other factors that may affect range or adoption.
- The 2026 observations are partial-year data and should not be interpreted as a complete-year decline.
- Model performance is evaluated on a random holdout from the same source population; external validation would be needed before operational use.

## Authors

- Anish Animesh Sahu
- Aryan Sanganti

K. J. Somaiya School of Engineering · Semester VII · 2026–27
