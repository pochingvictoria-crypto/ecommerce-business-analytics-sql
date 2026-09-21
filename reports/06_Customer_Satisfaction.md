# Chapter 6 – Customer Satisfaction

## 6.1 Business Question

How did customer satisfaction change as the business grew, and what issues were frequently mentioned in low-score reviews?

---

## 6.2 Data Validation

To evaluate customer satisfaction across comparable periods:

- Only delivered orders were included.
- January–August 2017 and January–August 2018 were used for year-over-year comparison.
- Review scores range from 1 to 5, with higher scores indicating higher customer satisfaction.
- Reviews with scores of 1–3 were classified as low-score reviews for text analysis.
- Only non-empty review comments were included in the text analysis.
- Portuguese stopwords and basic text preprocessing were applied before extracting frequently occurring phrases.

The relationship between delivery performance and review scores is observational and should not be interpreted as evidence of causality.

---

## 6.3 Average Review Score

Average review scores were compared between January–August 2017 and the same period in 2018.

```sql
SELECT
    strftime('%Y', o.order_purchase_timestamp) AS year,

    COUNT(DISTINCT o.order_id) AS reviewed_orders,

    ROUND(
        AVG(r.review_score),
        2
    ) AS avg_review_score

FROM orders o

JOIN order_reviews r
    ON o.order_id = r.order_id

WHERE
    o.order_status = 'delivered'

    AND strftime('%Y', o.order_purchase_timestamp)
        IN ('2017', '2018')

    AND strftime('%m', o.order_purchase_timestamp)
        BETWEEN '01' AND '08'

GROUP BY
    year

ORDER BY
    year;
```

| Period | Reviewed Orders | Avg. Review Score |
|---|---:|---:|
| Jan–Aug 2017 | 21,837 | 4.23 |
| Jan–Aug 2018 | 52,468 | 4.14 |

The average review score decreased slightly from **4.23 to 4.14**.

Although the overall review score remained relatively high, the decline suggests a modest deterioration in customer satisfaction during the same period in which the business expanded.

---

## 6.4 Review Score Distribution

Average scores alone may hide changes in the distribution of positive and negative customer experiences.

Therefore, the distribution of individual review scores was also examined.

```sql
SELECT
    strftime('%Y', o.order_purchase_timestamp) AS year,

    r.review_score,

    COUNT(*) AS reviews

FROM orders o

JOIN order_reviews r
    ON o.order_id = r.order_id

WHERE
    o.order_status = 'delivered'

    AND strftime('%Y', o.order_purchase_timestamp)
        IN ('2017', '2018')

    AND strftime('%m', o.order_purchase_timestamp)
        BETWEEN '01' AND '08'

GROUP BY
    year,
    r.review_score

ORDER BY
    year,
    r.review_score;
```

| Review Score | 2017 | 2018 |
|---:|---:|---:|
| 1 | 7.6% | 10.4% |
| 2 | 3.0% | 3.0% |
| 3 | 8.5% | 8.0% |
| 4 | 20.2% | 19.3% |
| 5 | 60.6% | 59.3% |

The proportion of **1-star reviews increased from 7.6% to 10.4%**, while the proportion of 5-star reviews decreased slightly from 60.6% to 59.3%.

Overall satisfaction therefore remained relatively high, but negative customer experiences became more frequent.

---

## 6.5 Delivery Performance and Review Score

Following the decline in on-time delivery performance identified in Chapter 5, review scores were compared between on-time and late deliveries.

```sql
SELECT

    CASE
        WHEN o.order_delivered_customer_date
             <= o.order_estimated_delivery_date
        THEN 'On-Time'
        ELSE 'Late'
    END AS delivery_status,

    COUNT(*) AS reviews,

    ROUND(
        AVG(r.review_score),
        2
    ) AS avg_review_score

FROM orders o

JOIN order_reviews r
    ON o.order_id = r.order_id

WHERE
    o.order_status = 'delivered'

    AND o.order_delivered_customer_date IS NOT NULL
    AND o.order_estimated_delivery_date IS NOT NULL

    AND strftime('%Y', o.order_purchase_timestamp)
        IN ('2017', '2018')

    AND strftime('%m', o.order_purchase_timestamp)
        BETWEEN '01' AND '08'

GROUP BY
    delivery_status;
```

| Delivery Status | Reviews | Avg. Review Score |
|---|---:|---:|
| Late | 5,774 | 2.59 |
| On-Time | 68,919 | 4.30 |

Orders delivered on time received an average review score of **4.30**, compared with only **2.59** for late deliveries.

This represents a substantial difference of **1.71 points** and indicates a strong association between delivery performance and customer satisfaction.

However, this relationship should not be interpreted as proof that late delivery alone caused lower review scores, since other factors may also influence customer evaluations.

---

## 6.6 Low-Score Review Analysis

To investigate the issues mentioned by dissatisfied customers, review comments with scores of **1–3** were analyzed.

A total of **9,750 non-empty low-score review comments** were included.

```sql
SELECT
    r.review_score,
    r.review_comment_message

FROM orders o

JOIN order_reviews r
    ON o.order_id = r.order_id

WHERE
    o.order_status = 'delivered'

    AND r.review_score <= 3

    AND r.review_comment_message IS NOT NULL
    AND TRIM(r.review_comment_message) != ''

    AND strftime('%Y', o.order_purchase_timestamp)
        IN ('2017', '2018')

    AND strftime('%m', o.order_purchase_timestamp)
        BETWEEN '01' AND '08';
```

After basic Portuguese text preprocessing, frequently occurring two-word phrases (bigrams) were extracted from the low-score comments.

| Phrase | Frequency |
|---|---:|
| nao recebi | 1,192 |
| recebi produto | 838 |
| ainda nao | 636 |
| produto nao | 633 |
| nao entregue | 366 |
| nao chegou | 355 |
| produto veio | 336 |
| ate agora | 322 |
| produto entregue | 217 |
| ate momento | 210 |

Several frequently occurring phrases relate to customers **not receiving products, products not arriving, or orders not being delivered**.

For example, `nao recebi` ("did not receive"), `nao entregue` ("not delivered"), and `nao chegou` ("did not arrive") appeared frequently in low-score comments.

These results indicate that delivery and order-fulfillment issues were prominent themes among dissatisfied customers.

---

## 6.7 Findings

- Average review score decreased slightly from **4.23 to 4.14**.
- The proportion of 1-star reviews increased from **7.6% to 10.4%**.
- Late deliveries received an average review score of **2.59**, compared with **4.30** for on-time deliveries.
- Delivery and order-fulfillment related expressions appeared frequently in low-score customer comments.
- Overall customer satisfaction remained relatively high, but negative customer experiences became more frequent during the observed growth period.

---

## 6.8 Business Insight

Customer satisfaction remained relatively high overall as the business expanded, but several indicators suggest increasing pressure on the customer experience.

Chapter 5 showed that average delivery time remained stable while the on-time delivery rate declined. This chapter further shows that late deliveries were associated with substantially lower review scores, while delivery- and fulfillment-related expressions appeared frequently in low-score customer comments.

Together, these findings suggest that **delivery and fulfillment reliability may be an important area for further investigation** as the business continues to scale.

However, the available data does not establish a causal relationship between business growth, delivery performance, and customer satisfaction. Additional analysis of seller performance, geography, logistics processes, and other operational factors would be required to identify the underlying drivers.
