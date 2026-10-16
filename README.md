<h1> Blue Bird: CBD Service Failures and Fleet Productivity Analysis </h1>

<div align="center">

🌐 [English](README.md) | [Indonesia](README_id.md)

[![Python](https://img.shields.io/badge/python-3.14%2B-blue)]()

</div>

## 1. Project Overview
This project analyzes three months of Blue Bird taxi operations data (October–December 2023) as a Data Analysis capstone. Blue Bird runs a physical fleet with permanent drivers, so every hour a car spends on duty without a passenger is a cost. The project brief raises three concerns: fleet productivity (*ritase*, trips per driver per day) may be declining, customer requests are not always fulfilled, and some corporate eVoucher trips happen at unusual times.

The primary focus is to **find when and where customer requests fail and how driver supply and shift schedules relate to those failures**, especially in the CBD during peak hours, while also checking fleet productivity and eVoucher usage.

Key Objectives:
- **Fleet optimization:** measure trips per shift, idle time, and differences between pools, and test whether *ritase* is really declining.
- **Service reliability:** map request failures by hour, zone, and request channel, and compare them with driver supply per hour.
- **B2B control:** describe eVoucher usage patterns and flag trips for review, without treating them as fraud.
- **Data reliability:** clean and validate the meter and service data before analysis, as required by the brief.

## 2. Data Sources
Six tables provided for the course in a MySQL database (not publicly available):
- `bb_trips` – Completed trips: times, distance, fare, payment method, service type, booking channel (318,098 rows)
- `bb_service_requests` – Customer requests from the App and Call Center, with status and wait time (289,622 rows)
- `bb_driver_shifts` – Driver shifts: clock-in/out, trip count, idle minutes (252,524 rows)
- `bb_drivers` – Driver attributes: pool, employment status, vehicle type, rating (4,055 rows)
- `bb_pools` – Pool (garage) location and zone affinity (8 rows)
- `bb_corporate_clients` – Corporate eVoucher clients and industry (286 rows)

## 3. Technologies Used
- Programming Language: Python (pandas, NumPy)
- Visualization: Matplotlib
- Statistics: SciPy, statsmodels
- Database access: PyMySQL, python-dotenv (credentials are read from a local `.env` file with `USER`, `PASS`, and `DATABASE`)
- Others: Jupyter Notebook

## 4. Project Structure

```
├── README.md                      <- This file.
│
├── README_id.md                   <- Indonesian version of this file.
│
├── analisis_bluebird_MAIN.ipynb   <- Main notebook: business understanding, cleaning,
│                                     exploration, visualization, exporting, conclusions.
│
└── outputs                        <- Created when the notebook runs.
    ├── raw                        <- The six source tables exactly as loaded (CSV).
    ├── cleaned                    <- The cleaned tables used in the analysis (CSV).
    ├── exceptions                 <- Excluded or flagged records with reasons, and eVoucher review lists.
    ├── reports                    <- Data quality reports and the statistical test summary.
    └── charts                     <- Charts saved from Part 3 (PNG).
```

Exported CSV files larger than 25 MB are split into numbered parts (for example `bb_trips_cleaned_part1.csv` to `bb_trips_cleaned_part5.csv`) with the same header, so they can be opened separately or combined in order.

The main notebook, [`analisis_bluebird_MAIN.ipynb`](analisis_bluebird_MAIN.ipynb), has four parts:
1. **Data Cleaning:** standardize service labels, flag data problems, and exclude 7,091 trips (2.2%) with a negative distance or negative booking-to-meter delay. Other tables keep all rows and use flag columns instead.
2. **Data Exploration:** request failures, driver supply, utilization, pools, eVoucher patterns, and statistical tests.
3. **Data Visualization:** charts for the main findings.
4. **Exporting:** raw, cleaned, and exception files.

## 5. Summary of Finding
### 5.1 Business Insight
- **Request failures are concentrated in the CBD at 07–08 and 17–18.** Overall, 6.7% of requests fail. CBD requests at these hours fail 36.6%, compared with 5.5% at other hours in the CBD. They are only 7.1% of all requests but 38.5% of all failures. The pattern repeats in December data that was not used to choose the hours (36.7% vs 5.5%).
- **The morning peak is not explained by the number of drivers on shift.** Drivers on shift rise from about 1,306 at 06:00 to 2,322 at 08:00, yet CBD failures stay high. The cause is not visible in the data; dispatch or driver readiness is a hypothesis that needs dispatch logs.
- **The evening peak coincides with the shift change.** Morning shifts end while evening shifts have not started. A capacity scenario (based on an assumed driver busy rate) shows a shortfall only around 16:00–17:00, so this is a hypothesis, not a proven cause.
- **App requests fail more often than Call Center requests** in every zone and hour group (22.45% vs 18.66% in the failure-peak hours).
- **Ritase is low but not declining.** About 1.26 trips per shift with no downward trend over 12 weeks; drivers spend 9.4% of on-duty time carrying passengers. Differences between pools disappear once the zone mix is taken into account (F-test p = 0.62).
- **eVoucher:** 51.4% of Airport eVoucher trips happen on weekends, compared with 17.6% of other Airport trips. No evidence of fraud was found, but there are no written usage rules; 1,290 trips are listed for review.

**Limitations:** only three months of data; all results are associations, not proven causes; the data's scale and very regular patterns suggest a sample or simulated dataset, so absolute numbers need confirmation; there are no dispatch logs or driver location data.

### 5.2 Actionable Recommendation
1. **Investigate the morning dispatch process before adding drivers:** check dispatch logs for 07–08, the time from clock-in to being ready for orders, and the App vs Call Center process.
2. **Trial a later end to morning shifts or short extra shifts (15:00–19:00) in the CBD**, compared against pools that do not take part.
3. **Trial fewer excess shift hours**, starting with the lowest revenue-per-hour zones, only after the data scale is confirmed, while monitoring failures.
4. **Agree written eVoucher rules with corporate clients** (days, hours, destinations) and review the 1,290 flagged trips with client admins.
5. **Evaluate pools using zone-adjusted trips per shift and revenue per hour**, not the `ritase_target_met` flag (met by only 0.35% of shifts).
6. **Improve data recording:** synchronize app and legacy system clocks, reject negative distances at input, and confirm the trips table is complete.

Baselines for each KPI are in the notebook; target values should be set by management.

## 6. Contact
- Name: _(to be added)_
- Email: _(to be added)_
- Linkedin: _(to be added)_
