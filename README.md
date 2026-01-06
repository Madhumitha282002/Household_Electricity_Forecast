# Household_Electricity_Forecast

## Background  

An energy company wants to review smart meter data from a single household to understand electricity consumption patterns and forecast future demand. The goal is to identify when and how much electricity is used, quantify seasonality, and build reliable short-term forecasts. These insights will help operations plan capacity and maintenance, and support product teams in designing tariffs and energy-saving programs for customers.  

## Executive Summary  

Analysis of household electricity usage reveals clear daily and weekly seasonality, with pronounced morning and evening peaks and lower demand overnight. Consumption is consistently higher during colder months, indicating strong weather sensitivity. Forecasting models (time-series based) achieve stable performance on the test period and are accurate enough for short-term planning and “what-if” analysis.  

- **Seasonality:** Strong daily and weekly patterns with higher winter demand.  
- **Peak Demand:** Peaks occur in the evening and on weekdays, increasing grid and cost pressure.  
- **Forecast Performance:** Time-series models provide low forecast error over the next 30 days.  
- **Operational Impact:** Results support capacity planning, tariff design, and energy-efficiency initiatives.  

Leveraging these forecasts helps prioritize demand-side management measures, reduce peak load risk, and improve customer-facing products.  

## Insights  

### 1. Consumption & Seasonality Trend  

Analysis of the time series highlights a stable base load with clear daily and weekly cycles. Electricity usage typically dips during late night hours, rises in the morning, drops slightly mid-day, and peaks again in the evening when occupants are most active. Weekdays tend to show higher and more regular consumption patterns compared to weekends. Over the year, consumption increases during colder months, reflecting heating and lighting needs, and remains relatively lower during milder seasons.  

![Daily & Weekly Trends](daily_weekly_trend.jpg)  

### 2. Forecast Performance & Model Comparison  

Multiple time-series models were evaluated to forecast daily electricity consumption (e.g., SARIMA and Exponential Smoothing/ETS). Models were trained on historical data and tested on a 30-day hold-out period. The final selected model achieved low error rates (e.g., single-digit percentage MAPE), capturing both trend and seasonality while maintaining stable residuals. This level of accuracy is sufficient for short-term operational planning and scenario analysis, such as anticipating high-demand days or testing the impact of new tariffs.  

![Forecast vs Actual](forecast_performance.jpg)  

### 3. Peak Demand & Risk Management  

The top peak-load days coincide with colder periods and high evening activity. These spikes significantly exceed average daily consumption and, if multiplied across many households, can strain grid capacity. Identifying these patterns allows the operations team to prepare for high-load windows, consider targeted demand-response programs, and optimize procurement strategies. For customers, aligning incentives (e.g., time-of-use tariffs) with peak periods can encourage shifting flexible loads to off-peak times, reducing both system stress and bills.  

![Peak Demand Analysis](peak_demand.jpg)  

### 4. Household Behavior & Efficiency Opportunities  

Hourly consumption profiles reveal a consistent base load from always-on devices and additional variable load from appliances used during specific times (cooking, entertainment, heating/cooling). The gap between base and peak consumption indicates potential savings through better scheduling of flexible activities (e.g., laundry, dishwashing) and adoption of more efficient devices. Personalized recommendations based on this forecast (such as “shift usage away from forecasted peak hours”) can improve customer engagement and help the company meet efficiency and sustainability targets.  

![Usage Profile & Opportunities](usage_profile.jpg)  
