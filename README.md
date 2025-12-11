Smart-Mobility-and-Traffic-Flow-Optimization-in-Austin  
TEAM A04 – Sambisha Godi, Li-Hsin Chang, Margaret Croghan, Steven Marathias, Tanish Puneeth

---

## Overview

This project analyzes **traffic flow in Austin, TX** using IoT **Bluetooth sensor data**.  
By linking millions of roadside detections into origin–destination (OD) trips, we study when and where congestion appears, which corridors carry the most demand, and how travel times change by direction and time of day.

Using **Python, SQL-style transformations, and Tableau**, we:

- Reconstruct trips between sensors
- Measure segment travel times and speeds
- Identify high-demand OD corridors
- Detect peak-hour slowdowns and directional asymmetries

The workflow includes data cleaning, schema design (ERD), trip construction, time-of-day aggregation, and visual analytics.

---

## Motivation

Cities invest heavily in roads and signal systems, but congestion patterns are often described anecdotally (“this road always backs up at 5 PM”).  
Bluetooth sensors provide **continuous, high-resolution data** on how drivers actually move through the network.

Our goals:

- Turn noisy sensor pings into **usable mobility insights**
- Understand whether **peak-hour slowdowns** are localized or system-wide
- Help planners answer: *Which corridors matter most, and when should we intervene?*

---

## Problem Definition

We want to understand **how traffic flows through Austin’s Bluetooth sensor network** and where bottlenecks form.

Key questions:

1. **Coverage & Reliability**  
   - Do we have enough detections and matched trips to trust the patterns?

2. **Temporal Congestion**  
   - When do speeds drop the most (AM vs PM peaks, weekday vs weekend)?

3. **Origin–Destination Movement**  
   - Q7: *What are the most common origins, destinations, and OD pairs?*  
   - Q8: *Which origin–destination routes are asymmetric (fast one way, slow the other)?*

4. **Planning Insight**  
   - Which specific segments or OD corridors should be **prioritized for signal retiming or capacity improvements**?

---

## Objectives

We structure the analysis around four pillars:

### 1. Network & Data Quality
- Validate detection coverage across sensors
- Filter out bad matches, missing IDs, and outlier travel times
- Ensure we have enough samples per segment / OD pair

### 2. Temporal Congestion Patterns
- Compare **hour-of-day** and **day-of-week** speed profiles
- Quantify how much slower peak-hour travel is vs off-peak
- Highlight segments with chronic, repeatable slowdowns

### 3. Origin–Destination Movement (Q7 & Q8)
- Rank **top origins**, **top destinations**, and **top OD pairs** by volume  
- Identify **directional asymmetry** where one direction is consistently slower
- Connect OD patterns back to real-world corridors

### 4. Segment-Level Planning Support
- Flag “hotspot” segments for further engineering study
- Provide a framework that can be reused as more sensors are added

---

## Data Sources

Primary data: **Austin Bluetooth Sensor Network**

Tables (conceptually):

- `DETECTION` – raw anonymized Bluetooth detections (device_id, sensor_id, timestamp, signal strength)
- `SENSOR` – sensor locations, road names, and directions
- `MATCHED_TRAVEL` – valid OD trips built by matching detections from the same device
- `SEGMENT` – road segments defined by origin and destination sensors plus distance
- `SUMMARY_INTERVAL` – time-bucketed averages (travel time, speed, sample size)

The notebook works from a cleaned version of these tables (e.g., `itmf_cleaned_outliers`) after removing obvious errors and extreme outliers.

---

## ER Diagram

Our ERD links five core entities:

- **SENSOR** ↔ **DETECTION** (each detection occurs at one sensor)  
- **MATCHED_TRAVEL** (joins two detections into a trip: origin → destination)  
- **SEGMENT** (maps sensor pairs to real-world road segments and distances)  
- **SUMMARY_INTERVAL** (aggregates segment performance over time windows)

#
<img width="965" height="931" alt="Screenshot 2025-12-11 at 5 11 34 PM" src="https://github.com/user-attachments/assets/4e780f13-4bc4-4da3-8126-8d7072c5eab2" />


---

## Data Cleaning Summary

We applied several cleaning and standardization steps before analysis:

### Detection / Matched Travel
- Dropped rows with missing IDs (device_id, sensor_id, timestamps)
- Removed trips with **negative or zero travel time**
- Removed extreme outliers based on travel-time and speed thresholds

### Segment / Summary Tables
- Verified that each segment has valid origin and destination sensors
- Computed **distance_miles** from GIS / provided metadata
- Aggregated trips into **time intervals** (e.g., hourly) with:
  - average travel time  
  - average speed  
  - sample size and standard deviation

The result is a **clean, analysis-ready OD and segment-level dataset**.

---

## Key Analytical Views

The notebook creates several intermediate “views” / tables to support analysis:

- **Trip-Level Views**
  - OD pairs with travel times and speeds
  - Filters for minimum sample sizes per OD pair

- **Temporal Views**
  - Average speed by segment × hour-of-day × weekday/weekend
  - Peak vs off-peak comparisons

- **OD Summary Views**
  - `top_origins` – ranked by number of trips  
  - `top_destinations` – ranked by number of trips  
  - `top_od_pairs` – OD pairs with highest volumes  
  - Directional performance comparison for each OD pair (A→B vs B→A)

These views feed all downstream charts and Tableau dashboards.

---

## Major Findings

### 1. Peak-Hour Slowdowns Are Clear and Recurrent

- Weekday mornings and evenings show **sharp drops in average speed** on major corridors.
- Speeds recover in late evening and on weekends, confirming **commuter-driven congestion**.
- A handful of segments carry a disproportionate share of slow traffic and should be **priority candidates for signal retiming**.

### 2. OD Traffic Is Concentrated on a Few Corridors

Using top-origin, top-destination, and top-OD-pair charts:

- A small set of origins and destinations account for a **large share of total trips**.
- These locations correspond to **major arterials and highway access points** in Austin.
- High-volume OD pairs form the **backbone of daily movement**, suggesting where monitoring and investment matter most.

### 3. Some OD Routes Are Directionally Asymmetric

By comparing travel times in both directions:

- Several routes are **consistently slower in one direction**, especially in peak periods.
- Likely drivers include:
  - Uneven lane capacity  
  - Signal timing that favors one direction  
  - Heavier inbound vs outbound demand at specific times
- These asymmetric corridors are **high-leverage opportunities** for direction-specific improvements (e.g., signal plans, turn lanes).

---

## Visualization Dashboard

A Tableau dashboard / story (link to be added by the team) summarizes:

- **Network overview and data coverage**
- **Top origins, destinations, and OD pairs**
- **Peak vs off-peak speed patterns**
- **Segment- and OD-level hotspot maps**

](https://public.tableau.com/app/profile/maggie.croghan/viz/Team4-FinalDashboard1/CongestionPatternsandSegmentPerformanceDashboard?publish=yes)
https://public.tableau.com/app/profile/lihsin.chang/viz/dashboard2_17652280043810/Dashboard2
---

## Technologies Used

- **Python** (pandas, numpy, matplotlib / seaborn) for data wrangling and visualization  
- **Jupyter Notebook** for analysis, documentation, and outputs  
- **Tableau Public** for interactive dashboards  
- **SQL-style transformations** (via pandas/BigQuery-style logic) for aggregations  
- **ERD tools** (e.g., Lucidchart / draw.io) for schema design

---

## Limitations

- Bluetooth detections are **opportunistic**: not every vehicle is captured, and detection rates may vary by location and time.
- Some segments have **low sample sizes**; patterns there should be interpreted cautiously.
- Travel times may be affected by **non-recurring events** (crashes, closures) that we do not explicitly model.
- Sensor coverage does not include every road in Austin, so we focus on corridors where data is reliable.

---

## Future Improvements

- Incorporate **additional data sources** (signal timing plans, incident logs, weather).  
- Build **predictive models** to forecast travel times or congestion probabilities.  
- Add **spatial visualizations** (map-based OD flows and hotspot maps).  
- Automate the pipeline so new sensor data can be ingested and updated regularly.

---

## Conclusion

This project demonstrates how **raw IoT sensor detections** can be transformed into **actionable mobility insights**:

- We quantify **when** congestion happens, **where** it is concentrated, and **which OD routes are most critical**.
- Planners can use these results to prioritize **signal tuning, corridor improvements, and sensor placement**.
- The workflow is repeatable and can scale as Austin expands its Bluetooth sensor network.

Overall, we turn high-volume detection data into a **practical decision-support tool** for smart mobility planning.
