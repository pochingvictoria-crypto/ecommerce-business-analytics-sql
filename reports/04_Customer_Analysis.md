# Chapter 4 – Customer Analysis

## 4.1 Business Question

How did the customer base change as the business grew, and how frequently did customers make repeat purchases?

---

## 4.2 Data Validation

To maintain consistency with the previous analyses:

- Only delivered orders were included.
- Customer growth was compared using **January–August 2017** and **January–August 2018**.
- `customer_unique_id` was used to identify individual customers.

In the Olist dataset, `customer_id` is associated with individual orders, while `customer_unique_id` can be used to identify the same customer across multiple purchases.

Therefore, `customer_unique_id` was used for both customer growth and purchase frequency analysis.

---

## 4.3 Customer Growth

Unique customers were compared between January–August 2017 and the same period in 2018.

```sql
SELECT
    strftime('%Y', o.order_purchase_timestamp) AS year,
    COUNT(DISTINCT c.customer_unique_id) AS unique_customers
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id
WHERE
    o.order_status = 'delivered'
    AND strftime('%Y', o.order_purchase_timestamp) IN ('2017', '2018')
    AND strftime('%m', o.order_purchase_timestamp) BETWEEN '01' AND '08'
GROUP BY year
ORDER BY year;
```

| Period | Unique Customers |
|---|---:|
| Jan–Aug 2017 | 21,404 |
| Jan–Aug 2018 | 51,612 |

The number of unique customers increased from **21,404** to **51,612**, representing an increase of approximately **141.1%**.

This indicates that the increase in business activity was accompanied by substantial expansion of the customer base.

---

## 4.4 Purchase Frequency

Purchase frequency was analyzed across the available dataset to understand how often individual customers completed purchases.

```sql
WITH customer_purchase AS (
    SELECT
        c.customer_unique_id,
        COUNT(DISTINCT o.order_id) AS purchase_count
    FROM orders o
    JOIN customers c
        ON o.customer_id = c.customer_id
    WHERE
        o.order_status = 'delivered'
    GROUP BY
        c.customer_unique_id
)

SELECT
    purchase_count,
    COUNT(*) AS customers
FROM customer_purchase
GROUP BY
    purchase_count
ORDER BY
    purchase_count;
```

| Completed Purchases | Customers |
|---:|---:|
| 1 | 90,557 |
| 2 | 2,573 |
| 3 | 181 |
| 4 | 28 |
| 5 | 9 |

Among **93,348 customers**, **2,791 customers completed two or more purchases**, representing approximately **2.99%** of customers.

Most customers therefore completed only one purchase during the observed dataset period.

---

## 4.5 Findings

- The number of unique customers increased from **21,404** to **51,612** between the comparable January–August periods, an increase of approximately **141.1%**.
- Most customers completed only one purchase during the available dataset period.
- Only **2.99%** of customers completed two or more purchases.
- Business growth was therefore accompanied by substantial customer-base expansion, while repeat purchasing remained limited.

---

## 4.6 Business Insight

The customer analysis indicates that business growth was associated primarily with expansion of the customer base.

Combined with the sales analysis in Chapter 2, the results suggest that increasing customer and transaction volumes were important components of business growth, while repeat purchasing remained limited during the observed dataset period.

However, the available observation period limits the ability to evaluate long-term customer retention. The low repeat-purchase rate should therefore not be interpreted as a direct measure of long-term customer loyalty.

Further analysis with a longer observation period or customer cohort data would be required to evaluate retention more accurately.

The next chapter examines whether delivery performance remained stable as order volume increased.
