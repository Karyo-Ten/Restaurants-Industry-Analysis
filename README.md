# Restaurants-Industry-Analysis

This project outlines the full SQL-based analytical work done on the restaurant dataset using **PostgreSQL**. It highlights schema design, SQL transformations, insights, and how the results were used for integration with BI tools like Power BI.

---

## 🗄️ Dataset Schema

After importing the dataset into PostgreSQL, we created a single table:

```sql
CREATE TABLE restaurants (
    id SERIAL PRIMARY KEY,
    name TEXT,
    city TEXT,
    rating FLOAT,
    rating_count INTEGER,
    cuisine TEXT,
    cost FLOAT,
    link TEXT
);
```

---

## 🧠 Key SQL Concepts & Transformations

### 🔍 1. Unique Cuisine Count
```sql
SELECT COUNT(DISTINCT unnest(string_to_array(cuisine, ', '))) AS unique_cuisines
FROM restaurants;
```
- **Goal**: Count all distinct cuisines across the dataset
- **Technique**: `string_to_array` + `unnest` to explode multi-cuisine strings

---

### 🌆 2. Cuisine Popularity by City
```sql
CREATE OR REPLACE VIEW cuisine_by_city AS
SELECT city, cuisine, COUNT(*) AS cuisine_count
FROM (
    SELECT city, unnest(string_to_array(cuisine, ', ')) AS cuisine
    FROM restaurants
) AS exploded
GROUP BY city, cuisine;
```
- **Goal**: View which cuisines are most common in each city
- **Use**: This view can be connected to Power BI for slicers and stacked charts

---

### ⭐ 3. City-Level Rating Summary
```sql
CREATE OR REPLACE VIEW city_rating_summary AS
SELECT city,
       ROUND(AVG(rating), 2) AS avg_rating,
       COUNT(*) AS restaurant_count,
       COUNT(DISTINCT name) AS unique_restaurants
FROM restaurants
GROUP BY city;
```
- **Purpose**: Understand average quality and market size in each city
- **Metrics**: Mean rating, total restaurants, distinct restaurant brands

---

### 🏪 4. Brand Popularity Across Cities
```sql
CREATE OR REPLACE VIEW brand_city_counts AS
SELECT name, city, COUNT(*) AS branch_count
FROM restaurants
GROUP BY name, city
ORDER BY branch_count DESC;
```
- **Goal**: Analyze restaurant chains and how widely they are spread

---

### 💰 5. Cost vs Rating Summary
```sql
CREATE OR REPLACE VIEW cost_rating_summary AS
SELECT city,
       ROUND(AVG(cost), 2) AS avg_cost,
       ROUND(AVG(rating), 2) AS avg_rating
FROM restaurants
GROUP BY city;
```
- **Purpose**: Determine if price levels correlate with higher ratings

---

## 📊 Integration with Power BI

These views (`cuisine_by_city`, `city_rating_summary`, etc.) were imported directly into Power BI using the PostgreSQL connector. This allowed:

- Reusable logic — only write SQL once, use many times
- Performance — PostgreSQL handles the computation before visual load
- Clean schema — only expose modeled data to Power BI users

---

## 🔍 Example Insights Discovered

- Delhi and Bangalore have the most cuisine diversity
- Some brands like Domino's Pizza operate in over 20 cities with hundreds of outlets
- Cities with mid-range cost restaurants generally have higher average ratings

---

## 🚀 How to Use

1. Import the `restaurants.csv` dataset into PostgreSQL.
2. Run the SQL scripts provided in `sql/schema.sql` and `sql/views.sql`
3. Query the views or expose them to Power BI

---

## 📂 Files
- `schema.sql` – Table creation and import guide
- `views.sql` – All SQL logic and views used
- `queries.sql` – Ad-hoc queries and data exploration

---

## 🧑‍💻 Author
**Name**: Himanshu Solanki
**Linkedin**: [linkedin.com/himanshusolanki](www.linkedin.com/in/himanshu-solanki-88b979288)

---

> "SQL transforms raw facts into business clarity."

