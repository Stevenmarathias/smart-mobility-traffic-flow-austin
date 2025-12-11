# Smart Mobility & Traffic Flow Optimization in Austin  
BA 815 – Final Team Project (Team A04)

This repository contains my personal copy of our BA 815 team project on **smart mobility in Austin** using **IoT Bluetooth sensor data**.

Team A04  
Sambisha Godi • Li-Hsin Chang • Margaret Croghan • Steven Marathias • Tanish Puneeth  

---

## Project Overview

The City of Austin deploys roadside Bluetooth sensors to anonymously detect devices and estimate travel times between key corridors. Our goal was to turn these detections into **actionable traffic insights**:

- Build **origin–destination (OD) trips** and measure travel times between sensor pairs.  
- Identify the **busiest origins, destinations, and OD pairs** across the network.  
- Detect **peak-hour slowdowns** and **directional asymmetries** (fast one way, slow the other).  
- Highlight **geographic hotspots** where congestion is concentrated.  
- Provide **data-driven recommendations** for signal timing, corridor improvements, and future sensor placement.

All heavy lifting (cleaning, feature engineering, analysis, and plots) lives in the Jupyter notebook in this repo.

---

## Data & ERD

We worked from the sensor schema below (tables renamed slightly for clarity):

- **SENSOR** – sensor id, location, road name, latitude/longitude, direction  
- **DETECTION** – raw Bluetooth pings (device_id, sensor_id, timestamp, signal strength)  
- **MATCHED_TRAVEL** – valid OD trips created by matching two detections from the same device, including start time, end time, and **travel_time_seconds**  
- **SEGMENT** – road segments defined by origin and destination sensors plus **distance_miles**  
- **SUMMARY_INTERVAL** – aggregated metrics for each segment and time window (average travel time, average speed, sample size, std. dev.)

This design lets us go from low-level pings → trips → segments → time-of-day patterns and planning insights.

---

## Key Questions

The analysis is structured around four main question groups:

1. **Data Quality & Coverage**  
   - Are detection volumes stable over time?  
   - Do we have enough matched trips per segment to trust the speed estimates?

2. **Temporal Congestion Patterns**  
   - When do **morning and evening peaks** occur?  
   - How much slower is peak-hour travel compared to off-peak?

3. **Origin–Destination (OD) Movement Insights**  
   - Q7. **What are the most common origins, destinations, and OD pairs?**  
   - Q8. **Which OD routes are asymmetric (fast one way, slow the other)?**

4. **Segment-Level Planning Insights**  
   - Which corridors show **chronic slow speeds**?  
   - Where are **peak-hour slowdowns concentrated geographically**?

Each question corresponds to a section in the notebook with dedicated code, charts, and text commentary.

---

## Highlights & Findings (Summary of Outputs)

This is a brief tour of what you will see when you open the notebook locally.

### 1. Data Quality & Volume

- Daily detection counts show **consistent activity** across most sensors, with a few obvious outliers that were excluded.  
- After cleaning, we retain **tens of thousands of matched trips**, enough to compute stable average speeds for key segments.  
- A small subset of segments has very low sample size; these are flagged and not used for strong conclusions.

### 2. Temporal Congestion Patterns

Using the `SUMMARY_INTERVAL` table, we:

- Plot **average speed by hour of day**, broken out by weekday vs. weekend.  
- Observe **clear AM and PM peaks** on weekdays, with speeds dropping noticeably during the commute windows.  
- Weekends show flatter patterns, confirming that congestion is primarily a **commuter phenomenon** rather than constant all day.

### 3. Origin–Destination Movement (Q7 & Q8)

**Q7 – Most Common OD Locations & Pairs**

- Bar charts of **Top Origins**, **Top Destinations**, and **Top OD Pairs** based on the number of matched trips.  
- The busiest origins and destinations cluster along a few major north–south and east–west corridors, indicating **key commuter gateways** into and across the network.  
- High-volume OD pairs form the **backbone of daily traffic**, and are natural priorities for any signal or capacity improvements.

**Q8 – Asymmetric Route Performance**

- For each OD pair with enough samples, we compare **travel times in both directions**.  
- Some routes are **noticeably slower in one direction**, especially during peak hours (for example, inbound morning commute vs. outbound evening).  
- These directional gaps suggest **uneven congestion, lane configuration differences, or signal timing that favors one direction**.  
- Flagging these segments helps planners target **direction-specific fixes** rather than treating both sides of the road the same.

### 4. Segment-Level Hotspots & Planning Ideas

- Heatmaps and summary tables highlight segments that are **consistently slow**, not just at one point in time.  
- We identify corridors that would benefit most from:
  - **Signal retiming** or better coordination,  
  - **Queue management** at key intersections,  
  - Or **additional detection coverage** to improve monitoring.

At the end of the notebook, we summarize **short-term recommendations** (signal optimization and monitoring) and **long-term ideas** (corridor redesign and better sensor placement).

---

## Repository Contents

- `A04-Smart-Mobility-and-Traffic-Flow-Optimization-in-Austin-Using-IoT-Bluetooth-Sensors.ipynb`  
  - Full project notebook with **all outputs included** (EDA, charts, tables, and written insights).  
- `README.md`  
  - This document – high-level summary, key findings, and instructions.  
- (Optional) `/figures/`  
  - If exported, static copies of important plots used in the final report.  
- (Optional) `/data/`  
  - Sample or schema files (the full raw datasets are not included due to size and course restrictions).

---

## Tools & Techniques

- **Language:** Python (pandas, numpy, matplotlib, seaborn)  
- **Environment:** Jupyter Notebook  
- **Visualization:** matplotlib / seaborn, plus Tableau for interactive dashboards  
- **Data Modeling:**  
  - ERD for SENSOR / DETECTION / MATCHED_TRAVEL / SEGMENT / SUMMARY_INTERVAL  
  - Trip construction from device-level detections  
  - Aggregation to time-interval summaries for speed and travel-time analysis
