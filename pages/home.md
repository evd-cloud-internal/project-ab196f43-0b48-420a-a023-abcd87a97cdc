---
name: Home
assetId: 9e5e7159-3669-429f-a047-9672ed2bfe54
type: page
---

# Orders Overview

{% big_value
  data="demo_daily_orders"
  value="sum(transactions)"
  title="Total Orders"
  fmt="#,##0"
/%}

{% big_value
  data="demo_daily_orders"
  value="sum(total_sales)"
  title="Total Revenue"
  fmt="usd1m"
/%}

{% big_value
  data="demo_daily_orders"
  value="avg(avg_transaction_value)"
  title="Avg Transaction Value"
  fmt="usd2"
/%}

## Orders Over Time

{% line_chart
    data="demo_daily_orders"
    x="date"
    y="sum(transactions)"
    date_grain="month"
    y_fmt="#,##0"
    title="Monthly Orders"
/%}

## Orders by Category

{% bar_chart
    data="demo_daily_orders"
    x="category"
    y="sum(transactions)"
    order="sum(transactions) desc"
    y_fmt="#,##0"
    title="Orders by Category"
/%}

## Revenue by Sales Channel

{% bar_chart
    data="demo_order_headers"
    x="sales_channel"
    y="count()"
    order="count() desc"
    y_fmt="#,##0"
    title="Orders by Sales Channel"
/%}

## Profitability by Item

```sql profitability
SELECT
    d.category,
    d.item_name,
    sum(d.quantity) AS total_qty,
    sum(d.quantity * d.unit_price) AS total_revenue,
    sum(d.quantity * i.cost) AS total_cost,
    sum(d.quantity * d.unit_price) - sum(d.quantity * i.cost) AS profit,
    (sum(d.quantity * d.unit_price) - sum(d.quantity * i.cost)) / sum(d.quantity * d.unit_price) AS margin
FROM demo_order_details d
JOIN demo_items i ON d.item_name = i.item_name AND d.category = i.category
GROUP BY d.category, d.item_name
ORDER BY profit ASC
```

{% table data="profitability" %}
    {% dimension value="category" /%}
    {% dimension value="item_name" title="Item" /%}
    {% measure value="sum(total_qty)" title="Qty Sold" fmt="#,##0" /%}
    {% measure value="sum(total_revenue)" title="Revenue" fmt="usd0" /%}
    {% measure value="sum(total_cost)" title="Cost" fmt="usd0" /%}
    {% measure value="sum(profit)" title="Profit" fmt="usd0" sort="asc" /%}
    {% measure value="avg(margin)" title="Margin" fmt="pct1" viz="color" /%}
{% /table %}
