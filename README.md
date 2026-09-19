# E-commerce Business Analytics with SQL & Python

## Project Overview

This project analyzes the Olist Brazilian E-commerce Dataset using SQL and Python to understand business growth, customer behavior, product performance, delivery operations, and customer satisfaction.

Rather than focusing only on SQL queries, the analysis starts from business questions and uses SQL for data extraction and aggregation, Python for visualization and text analysis, and business interpretation to connect findings across different areas of the business.

The analysis focuses particularly on how the business changed as transaction and customer volumes increased, and whether operational and customer experience indicators showed signs of pressure during the growth period.

---

## Objectives

- Translate business questions into measurable KPIs
- Extract and aggregate business data using SQL
- Analyze sales, product, customer, and operational performance
- Visualize key trends using Python
- Identify business insights while distinguishing correlation from causation
- Highlight data limitations and areas for further investigation

---

## Project Structure

### Chapter 1 – Executive KPI Dashboard
Provides an overview of key business metrics including revenue, orders, customers, Average Order Value, delivery rate, and review score.

### Chapter 2 – Sales Performance
Analyzes revenue, order volume, and Average Order Value to understand the main drivers of business growth.

### Chapter 3 – Product Analysis
Examines which product categories contributed most to product sales and order growth.

### Chapter 4 – Customer Analysis
Analyzes customer growth and purchase frequency to understand changes in the customer base and repeat purchasing behavior.

### Chapter 5 – Delivery Performance
Evaluates average delivery time and on-time delivery performance as order volume increased.

### Chapter 6 – Customer Satisfaction
Analyzes review scores, review distributions, the relationship between delivery performance and customer ratings, and frequently occurring terms in low-score customer comments.

### Chapter 7 – Overall Summary & Business Conclusion
Integrates findings across the analysis and summarizes key business observations, limitations, and areas for further investigation.

---

## Key Findings

- Business growth was primarily driven by increasing order volume, while Average Order Value remained relatively stable.
- Several major product categories contributed substantially to product sales growth.
- The customer base expanded considerably, while repeat purchasing remained limited during the observed period.
- Average delivery time remained stable as order volume increased, but the on-time delivery rate declined.
- Late deliveries were associated with substantially lower review scores.
- Delivery and order-fulfillment related expressions appeared frequently in low-score customer comments.

---

## Tools

- **SQL (SQLite)** – Data extraction, joins, aggregation, and KPI calculation
- **Python (pandas)** – Data processing and analysis
- **Matplotlib** – Data visualization
- **NLTK** – Portuguese stopword processing for customer review analysis
- **Google Colab** – Analysis environment

---

## Dataset

**Brazilian E-commerce Public Dataset by Olist**

The dataset contains information on orders, customers, products, sellers, payments, deliveries, and customer reviews from the Olist e-commerce platform in Brazil.

The available order data covers approximately 2016–2018. Because some periods are incomplete, year-over-year comparisons in this project primarily use January–August 2017 and January–August 2018 to maintain comparable observation periods.

---

## Analytical Approach

The project follows a business-oriented analytical workflow:

**Business Question → KPI Definition → SQL Analysis → Python Visualization → Findings → Business Insight**

SQL is used primarily to extract and aggregate the required business data, while Python is used when visualization or additional processing adds analytical value.

The analysis also explicitly considers data limitations and avoids interpreting observed relationships as causal when the available data does not support such conclusions.
