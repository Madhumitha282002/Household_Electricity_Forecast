## JMP Workflow

All analysis and forecasting for this project were performed in **JMP**, using its data preparation, visualization, and time series forecasting capabilities. Below is an overview of the end-to-end workflow.

---

### 1. Data Import & Preparation in JMP

1. **Importing the Data**
   - Open JMP → `File` → `Open…`
   - Select the raw text/CSV file containing the household electricity data (e.g., from the UCI dataset).
   - In the import dialog:
     - Ensure `Date` and `Time` are correctly interpreted as character columns.
     - Ensure numeric columns (e.g., `Global_active_power`) are set to **Numeric** with appropriate modeling type (usually *Continuous*).

2. **Creating a Proper Date/Time Variable**
   - Use `Cols` → `New Column…` to create a combined `Timestamp` column.
   - Set **Column Properties** → `Formula…` and build a formula such as:
     - `Parse Date( :Date || " " || :Time, "dd/mm/yyyy hh:mm:ss" )`
   - Set the new column’s data type to **Numeric** and format to **Date Time**.
   - This gives a single continuous time axis for plotting and aggregation.

3. **Handling Missing Values**
   - Go to `Analyze` → `Distribution` and select key variables such as `Global_active_power`.
   - Use the distribution output to identify missing or invalid values.
   - Options for handling missing values:
     - Filter out rows: use `Rows` → `Data Filter…` and exclude missing entries.
     - Or impute simple values (e.g., forward-fill) via `Cols` → `Standardize Attributes…` or by creating formula columns if needed.
   - For this project, rows with critical missing `Global_active_power` values are removed before aggregation.

4. **Aggregating to Daily Data**
   - Go to `Tables` → `Summary…`
   - Group by: `Date` (or a derived `Day` column from `Timestamp`).
   - For `Global_active_power`, choose **Mean** as the summary statistic.
   - This produces a new **daily-level** table with one row per day and a column such as `Mean_Global_active_power`.
   - Save this as the main time series table for modeling.

---

### 2. Exploratory Analysis in JMP

1. **Time Series Plot**
   - From the daily summary table, go to `Graph` → `Graph Builder`.
   - Drag the `Date` (or `Day`) column to the X-axis.
   - Drag `Mean_Global_active_power` to the Y-axis.
   - Turn on line view to visualize daily consumption over time and visually inspect seasonality and trends.

2. **Weekly & Monthly Patterns**
   - Create additional columns:
     - `Day of Week` using `Cols` → `New Column…` → `Formula` → `Day Of Week( :Date )`.
     - `Month` using `Month( :Date )` or `Year Month`.
   - Use `Graph Builder` (or `Analyze` → `Distribution`) to:
     - Plot average consumption by `Day of Week`.
     - Plot average consumption by `Month`.
   - This reveals **weekly** and **seasonal** patterns in the data.

3. **Autocorrelation (ACF/PACF)**
   - Go to `Analyze` → `Time Series` → `Time Series`.
   - Select the daily mean power column as the **Time Series** variable; specify **Date** as the time ID if requested.
   - Request **Autocorrelation** and **Partial Autocorrelation** in the platform options.
   - Use these plots to confirm:
     - Significant lags at multiples of 7 (weekly seasonality).
     - Appropriate AR/MA orders for modeling.

---

### 3. Building Time Series Models in JMP

#### a. Exponential Smoothing (ETS / Holt-type Models)

1. Open `Analyze` → `Time Series` → `Time Series Forecast` (or `Forecasting` platform, depending on JMP version).
2. Select:
   - Time ID: `Date`
   - Response: `Mean_Global_active_power`
3. In the **Model** options:
   - Choose **Exponential Smoothing**.
   - Let JMP automatically suggest a model, or manually test variants (e.g., Holt’s linear trend, with or without seasonal components).
4. Specify the **forecast horizon** (e.g., 30 days).
5. JMP outputs:
   - Fitted values vs. actuals
   - Forecasts with confidence intervals
   - Error metrics (e.g., RMSE, MAPE)
6. Inspect residual plots to ensure that:
   - Residuals are roughly white noise.
   - No obvious remaining structure is left unexplained.

#### b. SARIMA Modeling

1. Go to `Analyze` → `Time Series` → `ARIMA`.
2. Set:
   - Time ID: `Date`
   - Series: `Mean_Global_active_power`
3. Specify seasonal structure:
   - Seasonal period: **7** (for weekly pattern).
4. Start with candidate models informed by ACF/PACF, for example:
   - Non-seasonal (p, d, q) and seasonal (P, D, Q) values.
5. Use the **Fit Model** button and compare:
   - AIC / BIC values
   - Residual diagnostics, including autocorrelation of residuals and Ljung–Box tests.
6. Iterate until selecting a SARIMA model with:
   - Low AIC/BIC
   - Residuals that resemble white noise
   - Reasonable interpretability.

---

### 4. Train/Test Split & Model Evaluation

1. **Create a Holdout Set**
   - In the daily table, select the last 30 days as the holdout sample.
   - Use `Rows` → `Row Selection` or create a flag column (e.g., `Is_Test`) via a formula such as:
     - `:Date >= Date("YYYY-MM-DD")` for the first test day.
2. **Fit Models on Training Data Only**
   - Use a row filter or subset (`Tables` → `Subset…`) to create a training table without the holdout days.
   - Fit ETS and SARIMA models using the training table.
3. **Generate Forecasts**
   - In the Time Series Forecast / ARIMA platforms, specify a **Forecast Horizon of 30 days**.
   - Export forecasted values to a new table.
4. **Compare Forecasts with Actuals**
   - Join the forecast table with the original daily table on the Date column (`Tables` → `Join…`).
   - Create new columns to compute:
     - Forecast error: `Error = Actual - Forecast`
     - Absolute error, squared error, and percentage error.
   - Use `Analyze` → `Distribution` or `Tabulate` to summarize:
     - RMSE, MAE, MAPE for each model.
5. **Model Selection**
   - Choose the model with the lowest holdout error and well-behaved residuals (in this project, an ETS/Holt-type model).
   - Optionally, create a new column for an ensemble forecast (e.g., the average of SARIMA and ETS forecasts) and evaluate similarly.

---

### 5. Visualization & Reporting in JMP

1. **Overlay Forecast on Actuals**
   - Use `Graph Builder` with:
     - X-axis: `Date`
     - Y-axis: multiple variables (`Actual`, `ETS_Forecast`, `SARIMA_Forecast`)
   - Display lines for each series to visually compare model performance.
2. **Peak Demand Analysis**
   - Sort or filter by `Mean_Global_active_power` to identify top peak days.
   - Use bar charts or line charts to highlight peak periods and seasonal spikes.
3. **Script Saving**
   - Use `Red Triangle` menus in each JMP report to select `Save Script to Data Table` or `Save Script to Script Window`.
   - This ensures that all steps (data prep, modeling, and visualizations) can be rerun easily, supporting reproducibility.

---

### Summary of the JMP Workflow

1. **Import & clean** raw minute-level data.
2. **Aggregate** to daily averages using `Tables → Summary…`.
3. **Explore** seasonality and trends with `Graph Builder` and `Time Series` ACF/PACF.
4. **Model** using `Time Series Forecast` (Exponential Smoothing) and `ARIMA` (SARIMA).
5. **Evaluate** models on a 30-day holdout using error metrics and residual diagnostics.
6. **Visualize & report** results using overlay line charts, peak-demand views, and saved scripts.

This JMP-based workflow enables analysts and decision-makers to move from raw smart meter data to actionable, validated forecasts with minimal coding and highly visual, interactive tools.
