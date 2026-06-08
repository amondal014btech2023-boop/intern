# Analysis of HRD Underflow Solids

## Project Overview

This project focuses on analyzing the variation of **HRD (Hydraulic Reject/Recovery Device) Underflow Solids** with respect to key process parameters within the plant operation. The objective is to understand the relationship between process flow dynamics and HRD underflow solids, identify influential variables, and evaluate the effectiveness of engineered features in explaining solids behavior.

The study investigates the impact of:

* BOP Flow
* HRD Underflow Flow
* Delta Flow (Flow Imbalance)
* Tonnage Balance (Ton/H)
* Rolling Mean Features

---

# Dataset Description

The dataset consists of time-series process data collected from multiple BOP (Belt Operating Plant) units and HRD units.

### Primary Variables

| Variable            | Description                                                 |
| ------------------- | ----------------------------------------------------------- |
| BOP Flow            | Feed flow entering the HRD system                           |
| HRD Underflow       | Underflow flow exiting the HRD                              |
| HRD Solids          | Target variable representing underflow solids concentration |
| Solid Concentration | Percentage solids present in the stream                     |
| Timestamp           | Process observation time                                    |

The target variable for this analysis is:

**HRD Underflow Solids (%)**

---

# BOP–HRD Mapping Logic

The relationship between BOP units and HRDs is dynamic and depends on equipment availability and operating conditions.

## Mapping Rules

### BOP1

* BOP1 → HRD101
* BOP1 → HRD102 only when HRD101 is offline

### BOP2

* BOP2 → HRD102 when HRD201 is offline or connected to BOP3
* BOP2 → HRD201 when HRD102 is offline or connected to BOP1

### BOP3

* BOP3 → HRD301
* BOP3 → HRD201 only when HRD301 is offline

This mapping was applied throughout the analysis to ensure that each HRD is associated with the correct upstream BOP source.

---

# Data Preprocessing

## 1. Data Cleaning

To improve data quality and model reliability, outlier treatment was performed.

### Target Variable (HRD Solids)

Outliers were removed using percentile-based filtering:

* Lower threshold: 5th percentile
* Upper threshold: 95th percentile

### Feature Variables

Outliers were capped instead of removed:

* Values below the 5th percentile were capped at the 5th percentile.
* Values above the 95th percentile were capped at the 95th percentile.

This approach preserves data volume while reducing the influence of extreme values.

---

# Feature Engineering

Several derived parameters were created to better represent process behavior.

## Delta Flow

To capture the difference between incoming and outgoing material flow:

**Delta Flow = BOP Flow − HRD Underflow**

### Purpose

* Represents flow imbalance within the circuit.
* Indicates potential accumulation or depletion of material.
* Helps identify process inefficiencies.

---

## Tonnage (Ton/H)

Since solids concentration alone does not fully describe material movement, a mass-flow parameter was introduced.

### Formula

**Tonnage = (Flow × Solid Concentration) / 1000**

### Purpose

* Estimates solid mass flow rate.
* Provides a more realistic representation of material transport.

---

## Delta TonH

A differential tonnage parameter was created as:

**Delta TonH = BOP TonH − HRD TonH**

### Critical Observation

Although Delta TonH showed noticeable correlations with HRD solids, this relationship is not considered physically meaningful because:

* HRD solids are already part of the HRD tonnage calculation.
* This creates an inherent mathematical dependency.
* The resulting correlation may therefore be artificially inflated.

---

# Rolling Mean Features

Industrial process data often contains:

* Measurement noise
* Short-term disturbances
* Operational fluctuations

To reduce these effects, rolling mean (moving average) features were generated.

## Rolling Delta Flow

**Rolling Delta Flow = Rolling Mean(BOP Flow) − Rolling Mean(HRD Flow)**

### Benefits

* Reduces process noise
* Smoothens sudden spikes and drops
* Reveals underlying trends
* Improves interpretability during visualization and correlation analysis

---

# HRD-wise Dataset Preparation

After data cleaning and feature engineering, separate datasets were created for each HRD.

This ensures that each unit is analyzed independently using only its physically relevant process variables.

---

## Step 1: Apply Dynamic Mapping

The BOP–HRD mapping logic was first applied to correctly identify the active feed source for each HRD.

---

## Step 2: Identify Active HRD Conditions

Only periods where the HRD was actively operating were retained.

### Criteria

* HRD underflow values must be valid.
* Offline periods were excluded.
* Invalid operating conditions were removed.

---

## Step 3: Create HRD-Specific Datasets

Four independent datasets were generated:

* HRD101 Dataset
* HRD102 Dataset
* HRD201 Dataset
* HRD301 Dataset

Each dataset contains:

* HRD Solids
* BOP Flow
* HRD Underflow
* Delta Flow
* Tonnage Features
* Rolling Mean Features

---

# Exploratory Data Analysis (EDA)

EDA was performed independently for:

* HRD101
* HRD102
* HRD201
* HRD301

The objectives were:

* Understand process behavior
* Identify correlations
* Detect trends and seasonal effects
* Compare HRD performance across units

Correlation analysis and trend visualization were performed on both raw and engineered features.

---

# Key Findings

## HRD101

### Observations

* Strong positive correlation observed during **2025-05**.
* Overall correlation remains positive.

### Feature Relationships

* Delta TonH shows a negative correlation with HRD solids.
* Rolling Mean Delta Flow shows a positive correlation with Rolling Mean HRD solids.

---

## HRD102

### Observations

* Strong positive correlation observed during **2025-11**.
* Overall correlation remains positive.

### Feature Relationships

* Delta TonH shows a negative correlation with HRD solids.
* Rolling Mean Delta Flow shows a positive correlation with Rolling Mean HRD solids.

---

## HRD201

### Observations

* Strong negative correlation observed during **2025-05**.
* Overall correlation remains negative.

### Feature Relationships

* Negative correlation between Delta TonH and HRD solids.
* Very weak negative correlation between Rolling Mean Delta Flow and Rolling Mean HRD solids.

---

## HRD301

### Observations

* Strong negative correlation observed during **2025-12**.
* Overall correlation remains negative.

### Feature Relationships

* Negative correlation between Delta TonH and HRD solids.
* Very weak positive correlation between Rolling Mean Delta Flow and Rolling Mean HRD solids.

---

# Conclusion

The analysis demonstrates that the relationship between flow imbalance and HRD underflow solids varies significantly across HRD units.

Key conclusions include:

1. HRD101 and HRD102 generally exhibit positive relationships between flow imbalance and solids concentration.
2. HRD201 and HRD301 display predominantly negative relationships.
3. Rolling mean features provide more stable and interpretable trends compared to raw process variables.
4. Delta TonH consistently shows negative correlation with HRD solids; however, this relationship should be interpreted cautiously due to the inherent mathematical dependency in its formulation.
5. Dynamic BOP–HRD mapping is essential for accurately representing process connectivity and ensuring meaningful analysis.

Future work may include predictive modeling of HRD solids using engineered process features and evaluation of advanced time-series techniques.
