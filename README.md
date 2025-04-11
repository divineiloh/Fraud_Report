-- README
-- This Power BI-compatible data model is based on a multi-step SQL pipeline designed to track driver lifecycle events, fraud detection systems, onboarding metrics, and appeal outcomes.
-- The query builds and populates several analytical tables:
--
-- 1. `sample_table`: Main fact-like table with driver profiles, fraud flag dates, onboarding, pause status, trip counts, and CMS withdrawal flags.
-- 2. `sample_appeals`: Derived table joining appeal data to drivers who were deactivated. Prioritizes completed appeals and flags overturned cases.
-- 3. `final_table`: Joins `sample_table` and `sample_appeals`, adds KPI event timestamps (e.g., onboarding before Spark Now, post-Spark detection).
-- 4. `sample_unpivot`: Unpivots the fraud system flags for time-series aggregation. Categorizes by Prevention vs Detection.
-- 5. `appeal_unpivot`: Unpivots appeal sub-reason codes to weekly counts of appeals and overturns, grouped into higher-level categories.
-- 6. `summary_table`: Aggregates KPI metrics weekly, showing number of drivers/events per KPI.
--
-- These tables are used to:
-- - Evaluate fraud flag timing (before/after Spark Now).
-- - Count driver deactivations or withdrawals by fraud system.
-- - Track appeal success rates over time.
-- - Support prevention vs detection analysis for fraud teams.
-- - Feed Power BI dashboards via a structured, analyzable format.
--
-- Note: For Power BI, this model should be exported with masked or dummy data, and relationship diagrams (Model View) should be used to visualize joins.

-- This data model consists of a series of layered tables built for driver performance, fraud detection,
-- and KPI tracking in a delivery service environment.

-- Table 1: `sample_table`
-- Main aggregation of driver status, onboarding, fraud flags, spark now activity, pause status, and trips
-- Combines data from multiple source tables using left joins and computes indicators for fraud systems

-- Table 2: `sample_appeals`
-- Extracts and processes appeal information for deactivated drivers
-- Includes reason codes, overturn indicators, and timestamps related to appeals

-- Table 3: `final_table`
-- Enriches `sample_table` with appeal outcomes and constructs KPI dates
-- KPI fields track prevention and detection metrics by comparing driver activity and fraud flag timelines

-- Table 4: `sample_unpivot`
-- Unpivots fraud flag dates into rows by fraud system and categorizes them into Prevention vs Detection
-- Joins calendar week to fraud flag dates and aggregates counts of flagged, deactivated, and withdrawn drivers

-- Table 5: `appeal_unpivot`
-- Unpivots appeal data by week and sub-reason code into counts of total appeals and overturned appeals
-- Categorizes sub-reasons into broader appeal categories

-- Table 6: `summary_table`
-- Unpivots all KPI-relevant dates from `final_table`
-- Aggregates counts of relevant drivers or average trip counts based on KPI type
-- Groups all output by week and KPI type for visualization

-- Notes:
-- All tables are materialized via `create or replace table` statements
-- Data source tables: `Driver_table`, `activity_table`, `dat`, `fst`, `acc`, `rt`, `ink`, `per`, `graph`, `invest_table`, `shield`, `delivery_table`, `appeals_table`, `week_table`
-- The model tracks and measures both fraud detection (after Spark Now) and fraud prevention (before Spark Now)
-- Additional unpivoting structures are created to support reporting and dashboard use cases
