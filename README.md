# 💼 Sales Insights Data Analysis Project

## 📊 Project Overview

To boost my data analysis skills, I took on an end-to-end project where I explored the synergy between SQL and Power BI to uncover meaningful business insights. I selected a simulated dataset that reflects a computer hardware company navigating the challenges of a competitive and dynamic market.

In this project, I focused on writing SQL queries to identify patterns in sales performance, product profitability, customer behaviour, and regional market trends. Once I cleaned and organised the data, I used Power BI to build an interactive dashboard that offers a straightforward view of the key business metrics.

This project really opened my eyes to how data moves through various systems, how to ask the right questions, and how to present data-driven insights in a visually appealing way. I also got some hands-on experience with managing relational databases, executing join operations, and crafting visual narratives from raw data.

---

## 📌 Tools Used

- MySQL
- Power BI
- SQL
- Power Query

---

## 🗂️ Dataset

The analysis is performed using a MySQL database file: [`sales_db.sql`](./sales_db.sql)

The database consists of the following tables:

- `transactions`
- `products`
- `markets`
- `customers`
- `date`

---

## 🛠️ Setup Instructions

To set up the database on your local machine:

1. Install MySQL:  
   Follow the instructions in this [YouTube tutorial](https://www.youtube.com/watch?v=a3HJnbYhXUc&t=688s).

2. Import the database dump:  
   Use the `sales_db.sql` file to restore the database using MySQL Workbench or command line.

---

## 🧮 SQL Analysis

### 🔹 Basic Insights

1. **Distinct market zones:**
   ```sql
   SELECT DISTINCT sales.markets.zone FROM sales.markets;

2. **Product count by type:**
   ```sql
   SELECT product_type, COUNT(*) FROM sales.products GROUP BY sales.products.product_type;

3. **Transactions in 2018:**
   ```sql
   SELECT COUNT(*) FROM sales.transactions
   JOIN sales.date ON transactions.order_date = date.date
   WHERE sales.date.year = 2018;

4. **Transactions with negative profit margin:**
   ```sql
   SELECT * FROM sales.transactions
   WHERE profit_margin_percentage < 0;

---

### 🔹 Sales and Revenue Analysis

1. **Total sales amount per year:**
   ```sql
   SELECT date.year, SUM(transactions.sales_amount) AS total_sales_amount
   FROM sales.transactions
   INNER JOIN sales.date ON transactions.order_date = date.date
   GROUP BY date.year;

2. **Top 5 markets by total sales:**
   ```sql
   SELECT markets.markets_name, SUM(transactions.sales_amount) AS total_sales_amount
   FROM sales.transactions
   INNER JOIN sales.markets ON transactions.market_code = markets.markets_code
   GROUP BY markets.markets_code
   ORDER BY total_sales_amount DESC
   LIMIT 5;

3. **Average profit margin per product type:**
   ```sql
   SELECT products.product_type, AVG(transactions.profit_margin) AS avg_product_margin
   FROM sales.transactions
   JOIN sales.products ON transactions.product_code = products.product_code
   GROUP BY products.product_type;

4. **Most profitable customers in 2019:**
   ```sql
   SELECT customers.custmer_name, SUM(transactions.profit_margin) AS total_profit_margin
   FROM sales.transactions
   JOIN sales.date ON transactions.order_date = date.date
   JOIN sales.customers ON transactions.customer_code = customers.customer_code
   WHERE date.year = 2019
   GROUP BY customers.custmer_name
   ORDER BY total_profit_margin DESC;

5. **Monthly sales trend for 2018:**
   ```sql
   SELECT date.month_name, EXTRACT(MONTH FROM date.date) AS month_number, SUM(transactions.sales_amount) AS total_sales
   FROM sales.transactions
   INNER JOIN sales.date ON transactions.order_date = date.date
   WHERE date.year = 2018
   GROUP BY date.month_name, month_number
   ORDER BY month_number;

---

### 🔹 Join-Based Exploration

1. **Products sold in Delhi NCR with market zone:**
   ```sql
   SELECT DISTINCT t.product_code, p.product_type, m.markets_name
   FROM sales.transactions AS t
   JOIN sales.products AS p ON t.product_code = p.product_code
   JOIN sales.markets AS m ON t.market_code = m.markets_code
   WHERE m.markets_name = "Delhi NCR";

2. **Monthly transactions in 2019:**
   ```sql
   SELECT d.month_name, EXTRACT(MONTH FROM d.date) AS month_num, COUNT(*) AS transactions
   FROM sales.transactions t
   JOIN sales.date d ON t.order_date = d.date
   WHERE d.year = 2019
   GROUP BY d.month_name, month_num
   ORDER BY month_num;

3. **Total cost price per market:**
   ```sql
   SELECT m.markets_code, m.markets_name, SUM(t.cost_price) AS total_cost_price
   FROM sales.transactions t
   JOIN sales.markets m ON t.market_code = m.markets_code
   GROUP BY m.markets_code, m.markets_name;

4. **Transactions with Brick & Mortar customers and INR currency:**
   ```sql
   SELECT *
   FROM sales.transactions t
   JOIN sales.customers c ON t.customer_code = c.customer_code
   WHERE c.customer_type = "Brick & Mortar" AND t.currency = "INR";

---

### 🔹 Advanced Exploration

1. **Market with highest average profit per transaction:**
   ```sql
   SELECT m.markets_code, m.markets_name, AVG(t.profit_margin) AS avg_profit_per_trans
   FROM sales.transactions t
   JOIN sales.markets m ON t.market_code = m.markets_code
   GROUP BY m.markets_code, m.markets_name
   ORDER BY avg_profit_per_trans DESC
   LIMIT 1;

2. **Customers who purchased both Own Brand and Distribution products:**
   ```sql
   SELECT c.customer_code, c.custmer_name
   FROM sales.transactions t
   JOIN sales.customers c ON t.customer_code = c.customer_code
   JOIN sales.products p ON t.product_code = p.product_code
   GROUP BY c.customer_code, c.custmer_name
   HAVING COUNT(DISTINCT p.product_type) = 2;

3. **Top 3 most profitable products for each year:**
   ```sql
    SELECT year, product_code, total_profit
    FROM (
        SELECT d.year, p.product_code, SUM(t.profit_margin) AS total_profit,
               ROW_NUMBER() OVER (PARTITION BY d.year ORDER BY SUM(t.profit_margin) DESC) AS rank_temp
        FROM sales.transactions t
        JOIN sales.products p ON t.product_code = p.product_code
        JOIN sales.date d ON t.order_date = d.date
        GROUP BY d.year, p.product_code
    ) AS ranked_products
    WHERE rank_temp <= 3;

---

## 📈 Power BI Dashboard

In Power BI, I built a dashboard to visualize key sales metrics, market performance, product profitability, and customer trends.

## 🔍 1. Key Insights

This section focuses on high-level sales performance across markets and customer types (Brick & Mortar vs E-Commerce). It includes:

- Total Revenue and Sales Quantity KPIs.
- Revenue and Quantity by Market, segmented by customer type.
- Revenue Trend over Time showing monthly revenue flow.
- Top 5 Customers and Products by Revenue.

📌 Custom Measures Created:

```DAX
Revenue = SUM('sales transactions'[norm_sales_amount])
```
```DAX
Sales Qty = SUM('sales transactions'[sales_qty])
```

---

## 💡 2. Profit Analysis

This page dives into profit margins and contribution by market and customer.

- Profit Margin % by Market
- Revenue and Profit Contribution % by Market
- Interactive Revenue Trend chart with detailed breakdowns.
- Tabular view with Revenue Contribution, Profit Margin Contribution, and Profit Margin % for each customer.

📌 Custom Measures Created:

```DAX
Total Profit Margin = SUM('sales transactions'[profit_margin])
```
```DAX
Profit Margin % = DIVIDE([Total Profit Margin], [Revenue], 0)
```
```DAX
Revenue Contribution = 
DIVIDE([Revenue], 
    CALCULATE([Revenue], ALL('sales customers'), ALL('sales products'), ALL('sales markets'))
)
```
```DAX
Profit Margin Contribution = 
DIVIDE([Total Profit Margin], 
    CALCULATE([Total Profit Margin], ALL('sales customers'), ALL('sales products'), ALL('sales markets'))
)
```

---

## 🚦 3. Performance Analysis

This report highlights overall performance segmented by market zone, with drill-down functionality and dynamic visuals:

- Revenue Contribution % by Market Zone, with colour-coded performance based on a profit margin target set using a slider.
- Drill-down hierarchy: Zone → Market → Customer.
- Revenue Trend with Year-over-Year Comparison, showing both revenue and profit margin % trends over time.
- Interactive filters and cross-highlighting enabled for flexible analysis.

📌 Custom Measure for YoY Comparison:

```DAX
Revenue LY = CALCULATE([Revenue], SAMEPERIODLASTYEAR('sales date'[Date]))
```

---

## ✅ Key Takeaways

- Developed comprehensive SQL queries for slicing data across time, geography, and customer segments.

- Designed insightful Power BI visualisations for business users to quickly identify performance trends.

- Gained hands-on experience in a real-world end-to-end data analysis workflow.
   
