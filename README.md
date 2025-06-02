# Veichi-Financial-Sizing-Hourly-Block



This repository replicates and validates a solar pump hourly performance and financial yield model initially developed in Microsoft Excel. It simulates the performance of a solar-powered pump drive throughout the year, accounting for varying sunshine availability, inverter minimum operating limits, pump motor size, and efficiency losses.

## Purpose

The goal of this project is to model and validate daily solar energy yields for a water pumping system using Python, with results that closely match a carefully constructed Excel spreadsheet. The model is designed to support financial viability assessments for irrigation systems powered by solar variable speed drives.

---

## Key Features

* Inputs sunshine profile across 13 daylight hours (06:00–18:00) on a clear day.
* Allows solar array size and pump motor kW to vary.
* Computes:

  * Sizing ratio between solar array and pump
  * Drive output capped at 100%
  * Pump operational limits (minimum drive frequency)
  * Energy losses (user-defined percentage)
* Calculates daily energy yield for each month using spread logic matched to Excel.
* Fully replicates Excel logic:

  * Noon-centered hour spreading of available pump hours (L29\:X41)
  * Daily energy yield based on available pump hours and hourly drive capability (L43\:X43)
  * Integration with total pumping hours per month (L27\:X27)

---

## Excel Logic Overview

The Excel file contains the following logic structure in the `Irrigation Technical` sheet:

1. **A29\:A41** — Hours of the day (hardcoded, 06:00 to 18:00)
2. **B29\:B41** — Sunshine availability per hour on a clear day (percent)
3. **D29\:D41** — Sizing Factor = Sunshine% × (Solar kW ÷ Pump Motor kW)
4. **E29\:E41** — Drive Output = `MIN(D, 1.0)`
5. **F29\:F41** — Pump Output Power (W) = Pump kW × 1000
6. **G29\:G41** — Minimum Drive Hz limit: if Drive Output < threshold (e.g. 35%), output = 0
7. **H29\:H41** — Apply loss factor to calculate effective output
8. **I29\:I41** — Hourly usable pump output = H ÷ F
9. **L27\:X27** — Total pumping hours available per month ## this values should be extracted from the Financial-Sizing-Monthly-Block
10. **L29\:X41** — Hourly spread of those total hours centered around 12:00
11. **L43\:X43** — Daily energy yield = `SUMPRODUCT(I29:I41, L29:X41)`

---

## What We Learned

* Translating structured Excel logic into Python requires careful attention to rounding, min/max constraints, and the cascading effect of logic blocks.
* Edge cases (e.g., total pump hours < 1) must be explicitly handled to avoid overestimating yield.
* Excel tends to prioritize full-hour allocations before fractional ones. This logic was successfully replicated with a Python spreading algorithm.
* A consistent and structured approach improves maintainability and allows for real-world use in project design, quoting, and feasibility modeling.

---

## Input Assumptions

* **Sunshine Profile**: Based on percent of clear-sky irradiance across 13 daylight hours.
* **Solar Array Size (kW)**: Adjustable input (e.g., 15.54 kW).
* **Pump Motor kW**: Adjustable input (e.g., 11, 15, or 20 kW).
* **Loss Factor**: Default 2% (adjustable).
* **Drive Min %**: Minimum inverter output allowed (e.g., 35%).

---

## Output

* `spread_df` contains a 15-row table per month:

  * 13 hourly spread values (L29\:X41)
  * Total hours per month (L27\:X27)
  * Daily output in kWh (L43\:X43)

---


The script outputs a Pandas DataFrame with hourly spread, total monthly hours, and final daily yield.

---

## Future Enhancements

* Add support for cumulative monthly and annual kWh.
* Include pump flow mapping for estimating total m³/day.
* Visualize with matplotlib or plotly.
* Wrap as a Python module or Streamlit web app for field engineers.

---


