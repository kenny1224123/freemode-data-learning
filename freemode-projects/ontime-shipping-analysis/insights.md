# 📈 出貨準時率分析系統 Insights 報告

本報告彙整專案中八大分析模組的核心發現與商業建議，可作為簡報、履歷、Portfolio 使用的文字敘述來源。

---

## 模組 #1️⃣ 地區 × 準時率分析

### ✅ 發現：
（請填入觀察結果）

### 💡 建議：
（例如：北部出貨最穩定，建議中南部物流強化）

### 💻 Query：
```sql
SELECT 
  c.region,
  COUNT(*) AS total_orders,
  ROUND(SUM(CASE WHEN o.on_time = 'Y' THEN 1 ELSE 0 END) / COUNT(*) * 100, 2) AS on_time_rate
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
GROUP BY c.region;
```

---

## 模組 #2️⃣ 運輸方式 × 準時率分析

### ✅ 發現：

### 💡 建議：

### 💻 Query：
```sql
SELECT 
  s.shipping_method,
  COUNT(*) AS total_orders,
  ROUND(SUM(CASE WHEN o.on_time = 'Y' THEN 1 ELSE 0 END) / COUNT(*) * 100, 2) AS on_time_rate
FROM shipments s
JOIN orders o ON o.shipment_id = s.shipment_id
GROUP BY s.shipping_method;
```

---

## 模組 #3️⃣ 商品單價區間 × 準時率分析

### ✅ 發現：

### 💡 建議：

### 💻 Query：
```sql
SELECT 
  CASE
    WHEN p.unit_price < 100 THEN 'Low'
    WHEN p.unit_price BETWEEN 100 AND 500 THEN 'Medium'
    ELSE 'High'
  END AS price_range,
  COUNT(*) AS total_orders,
  ROUND(SUM(CASE WHEN o.on_time='Y' THEN 1 ELSE 0 END) / COUNT(*) * 100, 2) AS on_time_rate
FROM products p
JOIN orders o ON p.product_id = o.product_id
GROUP BY price_range;
```

---

## 模組 #4️⃣ 出貨月份 × 準時率趨勢分析

### ✅ 發現：

### 💡 建議：

### 💻 Query：
```sql
SELECT
  DATE_FORMAT(ship_date, '%Y-%m') AS month,
  COUNT(*) AS total_orders,
  ROUND(SUM(CASE WHEN on_time = 'Y' THEN 1 ELSE 0 END) / COUNT(*) * 100, 2) AS on_time_rate
FROM orders
GROUP BY month
ORDER BY month;
```

---

## 模組 #5️⃣ 延遲天數統計分析

### ✅ 發現：

### 💡 建議：

### 💻 Query（每筆延遲天數）：
```sql
SELECT 
  order_id,
  delivery_date,
  expected_delivery_date,
  DATEDIFF(delivery_date, expected_delivery_date) AS delay_days
FROM orders
WHERE delivery_date > expected_delivery_date;
```

### 💻 Query（延遲統計總覽）：
```sql
SELECT
  COUNT(*) AS total_delays,
  ROUND(AVG(DATEDIFF(delivery_date, expected_delivery_date)), 2) AS avg_delay_days,
  MAX(DATEDIFF(delivery_date, expected_delivery_date)) AS max_delay_days
FROM orders
WHERE delivery_date > expected_delivery_date;
```

---

## 模組 #6️⃣ Top 客戶分析（回購 / 準時）

### ✅ 發現：

### 💡 建議：

### 💻 Query（回購最多）：
```sql
SELECT 
  c.name,
  COUNT(*) AS total_orders
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.name
ORDER BY total_orders DESC;
```

### 💻 Query（準時最多）：
```sql
SELECT 
  c.name,
  SUM(CASE WHEN o.on_time='Y' THEN 1 ELSE 0 END) AS on_time_orders
FROM customers c
JOIN orders o ON c.customer_id=o.customer_id
GROUP BY c.name
ORDER BY on_time_orders DESC;
```

---

## 模組 #7️⃣ 商品類別 × 準時率分析

### ✅ 發現：

### 💡 建議：

### 💻 Query：
```sql
SELECT 
  p.category,
  COUNT(*) AS total_orders,
  ROUND(SUM(CASE WHEN o.on_time='Y' THEN 1 ELSE 0 END)/COUNT(*)*100,2) AS on_time_rate
FROM products p
JOIN orders o ON p.product_id=o.product_id
GROUP BY p.category;
```

---

## 模組 #8️⃣ 顧客 × 延遲分布區間分析

### ✅ 發現：

### 💡 建議：

### 💻 Query：
```sql
SELECT 
  c.name AS customer_name,
  CASE
    WHEN DATEDIFF(o.delivery_date,o.expected_delivery_date) BETWEEN 0 AND 1 THEN '0-1 days'
    WHEN DATEDIFF(o.delivery_date,o.expected_delivery_date) BETWEEN 2 AND 3 THEN '2-3 days'
    WHEN DATEDIFF(o.delivery_date,o.expected_delivery_date) BETWEEN 4 AND 6 THEN '4-6 days'
    ELSE '7+ days'
  END AS delay_group,
  COUNT(*) AS delay_count
FROM orders o
JOIN customers c ON o.customer_id=c.customer_id
WHERE o.delivery_date > o.expected_delivery_date
GROUP BY delay_group, customer_name
ORDER BY delay_group;
```

---

🧠 小提醒：每一段「✅ 發現」請具體描述數據呈現的趨勢，「💡 建議」則從商業角度給出可行行動方案。

可自由擴充成 Notion 簡報、PowerPoint 架構，或面試時當作案例說明！

### ✅ 發現：
目前樣本數過少，僅作為語法驗證與分析架構搭建。

### 💡 建議：
待導入真實歷史訂單資料後，重新執行查詢以產出實際洞察。
