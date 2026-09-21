# Chapter 3 – Product Analysis

## 3.1 Business Question

Which product categories contributed most to the company's sales growth?

---

## 3.2 Data Validation

To compare product performance across comparable periods, the analysis focuses on **January–August 2017** and **January–August 2018**.

- Only delivered orders were included.
- Product categories were translated into English using the `product_category_name_translation` table.
- Product sales were calculated using `order_items.price`.
- Freight costs were excluded from product sales.

Because payment values are recorded at the order level and an order may contain products from multiple categories, payments cannot be uniquely assigned to individual product categories.

Therefore, this chapter uses **Product Sales** rather than company-level Revenue when comparing category performance.

---

## 3.3 Product Sales Growth by Category

Product sales were aggregated by category and compared between January–August 2017 and the same period in 2018.

```sql
SELECT
    pct.product_category_name_english AS category,

    CASE
        WHEN strftime('%Y', o.order_purchase_timestamp) = '2017' THEN '2017'
        WHEN strftime('%Y', o.order_purchase_timestamp) = '2018' THEN '2018'
    END AS year,

    SUM(oi.price) AS product_revenue

FROM orders o

JOIN order_items oi
    ON o.order_id = oi.order_id

JOIN products p
    ON oi.product_id = p.product_id

JOIN product_category_name_translation pct
    ON p.product_category_name = pct.product_category_name

WHERE
    o.order_status = 'delivered'
    AND strftime('%m', o.order_purchase_timestamp) BETWEEN '01' AND '08'

GROUP BY category, year
ORDER BY category;
```

Absolute sales increase was used as the primary measure for ranking category growth, while percentage growth was used as supporting context.

---

## 3.4 Completed Order Growth by Category

Completed orders were analyzed to determine whether categories with strong product sales growth also experienced increasing transaction volume.

```sql
SELECT
    pct.product_category_name_english AS category,

    CASE
        WHEN strftime('%Y', o.order_purchase_timestamp) = '2017' THEN '2017'
        WHEN strftime('%Y', o.order_purchase_timestamp) = '2018' THEN '2018'
    END AS year,

    COUNT(DISTINCT o.order_id) AS completed_orders

FROM orders o

JOIN order_items oi
    ON o.order_id = oi.order_id

JOIN products p
    ON oi.product_id = p.product_id

JOIN product_category_name_translation pct
    ON p.product_category_name = pct.product_category_name

WHERE
    o.order_status = 'delivered'
    AND strftime('%m', o.order_purchase_timestamp) BETWEEN '01' AND '08'

GROUP BY category, year
ORDER BY category;
```

Absolute order increase was then calculated for each category and used to identify the categories contributing most to transaction growth.

---

## 3.5 Category Growth Comparison

<!-- INSERT: ch03_category_growth.png -->

The comparison shows that several major categories experienced substantial increases in both product sales and completed orders.

**Health & Beauty** recorded the largest absolute increase in product sales, increasing from approximately **BRL 243,522 to BRL 755,725**, an increase of approximately **BRL 512,203**.

**Watches & Gifts, Sports & Leisure, Computers & Accessories, Bed Bath & Table, and Housewares** also recorded substantial increases in product sales.

Some smaller categories recorded very high percentage growth rates. However, these percentages were partly influenced by relatively small sales bases in 2017. Therefore, absolute growth was used as the primary measure when evaluating contribution to overall business growth.

---

## 3.6 Findings

- Health & Beauty recorded the largest absolute increase in product sales.
- Several major categories, including Watches & Gifts, Sports & Leisure, and Computers & Accessories, also contributed substantially to growth.
- Major categories generally showed increases in both product sales and completed orders.
- High percentage growth in smaller categories should be interpreted carefully because of their smaller initial sales bases.

---

## 3.7 Business Insight

Product sales growth was distributed across several major categories rather than being concentrated in a single category.

The similar growth patterns in product sales and completed orders indicate that category growth was largely associated with increasing transaction volume.

Together with the sales analysis in Chapter 2, these results show that the company's growth was supported by increasing order activity across multiple major product categories.

The next chapter examines how the customer base changed during this period and whether customers primarily made one-time or repeat purchases.
