# Chapter 5 – Delivery Performance

## 5.1 Business Question

How did delivery performance change as order volume increased?

---

## 5.2 Data Validation

To evaluate delivery performance across comparable periods:

- Only delivered orders were included.
- January–August 2017 and January–August 2018 were used for comparison.
- Orders without an actual delivery date were excluded from the delivery-time analysis.
- Orders without an estimated delivery date were also excluded from the on-time delivery analysis.

Delivery time was measured as the number of days between the order purchase timestamp and the actual customer delivery timestamp.

An order was considered **on time** when the actual delivery date was on or before the estimated delivery date.

---

## 5.3 Average Delivery Time

Average delivery time was calculated to examine whether delivery speed changed as order volume increased.

```sql
SELECT
    strftime('%Y', order_purchase_timestamp) AS year,

    COUNT(*) AS delivered_orders,

    ROUND(
        AVG(
            julianday(order_delivered_customer_date)
            - julianday(order_purchase_timestamp)
        ),
        2
    ) AS avg_delivery_days

FROM orders

WHERE
    order_status = 'delivered'

    AND order_delivered_customer_date IS NOT NULL

    AND strftime('%Y', order_purchase_timestamp)
        IN ('2017', '2018')

    AND strftime('%m', order_purchase_timestamp)
        BETWEEN '01' AND '08'

GROUP BY
    year

ORDER BY
    year;
```

| Period | Delivered Orders | Avg. Delivery Days |
|---|---:|---:|
| Jan–Aug 2017 | 21,997 | 12.18 |
| Jan–Aug 2018 | 52,777 | 12.14 |

Delivered order volume increased from **21,997 to 52,777**, an increase of approximately **139.9%**.

Despite this substantial increase in order volume, average delivery time remained almost unchanged at approximately **12 days**.

---

## 5.4 On-Time Delivery Performance

Average delivery time alone does not indicate whether individual orders were delivered within the expected delivery window.

Therefore, on-time delivery performance was also analyzed.

```sql
SELECT
    strftime('%Y', order_purchase_timestamp) AS year,

    COUNT(*) AS delivered_orders,

    SUM(
        CASE
            WHEN order_delivered_customer_date
                 <= order_estimated_delivery_date
            THEN 1
            ELSE 0
        END
    ) AS on_time_orders,

    ROUND(
        SUM(
            CASE
                WHEN order_delivered_customer_date
                     <= order_estimated_delivery_date
                THEN 1
                ELSE 0
            END
        ) * 100.0 / COUNT(*),
        2
    ) AS on_time_rate

FROM orders

WHERE
    order_status = 'delivered'

    AND order_delivered_customer_date IS NOT NULL
    AND order_estimated_delivery_date IS NOT NULL

    AND strftime('%Y', order_purchase_timestamp)
        IN ('2017', '2018')

    AND strftime('%m', order_purchase_timestamp)
        BETWEEN '01' AND '08'

GROUP BY
    year

ORDER BY
    year;
```

| Period | Delivered Orders | On-Time Orders | On-Time Rate |
|---|---:|---:|---:|
| Jan–Aug 2017 | 21,997 | 21,077 | 95.82% |
| Jan–Aug 2018 | 52,777 | 47,833 | 90.63% |

While average delivery time remained stable, the on-time delivery rate declined from **95.82% to 90.63%**, a decrease of **5.19 percentage points**.

This indicates that overall delivery speed remained stable, while delivery reliability weakened during the same period.

---

## 5.5 Findings

- Delivered order volume increased by approximately **139.9%**, from 21,997 to 52,777.
- Average delivery time remained almost unchanged at approximately **12 days**.
- The on-time delivery rate declined from **95.82% to 90.63%**.
- Delivery speed therefore remained stable, while the ability to consistently meet estimated delivery dates weakened.

---

## 5.6 Business Insight

The delivery analysis shows that the business handled substantially higher order volume without a meaningful increase in average delivery time.

However, the decline in on-time delivery rate indicates that maintaining delivery reliability became more challenging during the same period.

The available data does not establish that higher order volume caused the decline in on-time performance. Other factors, such as logistics conditions, seller performance, geographic differences, or changes in estimated delivery dates, may also have contributed.

The next chapter examines whether delivery performance was associated with customer satisfaction and which issues appeared frequently in low-score customer reviews.
