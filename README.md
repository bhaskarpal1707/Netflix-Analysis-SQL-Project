# 📊 Netflix Data Analysis with SQL

## 📁 Project Overview

This repository presents a comprehensive SQL-based analysis of Netflix's media content, including both movies and TV shows. By utilizing advanced SQL queries, the project explores various business problems to derive actionable insights from the data.

The dataset, sourced from Kaggle, includes over 8,000 records with fields such as title, director, cast, country, release year, duration, and genre. The aim is to answer critical business questions related to content distribution, popularity, regional trends, and keyword-based categorization.

> **Dataset Source:** [Netflix Shows on Kaggle](https://www.kaggle.com/datasets/shivamb/netflix-shows)

---

## 🌟 Project Objectives

The project focuses on solving specific analytical objectives:

* Analyze the **distribution of content** by type (Movies vs. TV Shows).
* Identify the **most common content ratings**.
* Analyze content based on **release years, countries, and duration**.
* Explore genres and categorize content using **keywords in descriptions**.
* Provide business insights through SQL-driven exploration.

---

## 📃 Dataset Schema

```sql
CREATE TABLE netflix (
    show_id     VARCHAR(6),
    type        VARCHAR(10),
    title       VARCHAR(150),
    director    VARCHAR(208),    
    casts       VARCHAR(1000),
    country     VARCHAR(150),
    date_added  VARCHAR(50),
    release_year INT,    
    rating      VARCHAR(10),
    duration    VARCHAR(15),    
    listed_in   VARCHAR(100),    
    description VARCHAR(250)
);
```

---

## 🧰 Business Problems & SQL Solutions

### 1. **Distribution of Movies vs TV Shows**

```sql
SELECT type, COUNT(*) FROM netflix GROUP BY type;
```

*Insight:* Determines how Netflix balances its content library.

### 2. **Most Common Rating by Type**

```sql
SELECT type, rating, cnt FROM (
    SELECT type, rating, COUNT(*) AS cnt,
           RANK() OVER (PARTITION BY type ORDER BY COUNT(*) DESC) AS ranking
    FROM netflix
    GROUP BY type, rating
) AS t1
WHERE ranking = 1;
```

*Insight:* Highlights the rating most often assigned to each content type.

### 3. **Movies Released in a Specific Year (e.g., 2020)**

```sql
SELECT * FROM netflix WHERE type = 'Movie' AND release_year = 2020;
```

### 4. **Top 5 Countries by Content Volume**

```sql
SELECT UNNEST(STRING_TO_ARRAY(country, ',')) AS new_country,
       COUNT(show_id) AS total_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

### 5. **Longest Movie**

```sql
SELECT * FROM netflix WHERE type = 'Movie' AND duration = (
    SELECT MAX(duration) FROM netflix
);
```

### 6. **Content Added in the Last 5 Years**

```sql
SELECT * FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```

### 7. **Content by 'Rajiv Chilaka'**

```sql
SELECT * FROM netflix WHERE director ILIKE '%Rajiv Chilaka%';
```

### 8. **TV Shows with More Than 5 Seasons**

```sql
SELECT * FROM netflix
WHERE type = 'TV Show' AND SPLIT_PART(duration, ' ', 1)::numeric > 5;
```

### 9. **Content Count by Genre**

```sql
SELECT UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
       COUNT(show_id) AS total_content
FROM netflix
GROUP BY 1;
```

### 10. **Top 5 Years with Highest Avg Content from India**

```sql
SELECT EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) AS year,
       COUNT(*) AS yearly_content,
       ROUND(COUNT(*)::numeric /
            (SELECT COUNT(*) FROM netflix WHERE country = 'India')::numeric * 100, 2) AS avg_count_per_year
FROM netflix
WHERE country = 'India'
GROUP BY 1
ORDER BY avg_count_per_year DESC
LIMIT 5;
```

### 11. **Documentary Movies**

```sql
SELECT * FROM netflix WHERE listed_in ILIKE '%documentaries%';
```

### 12. **Content without a Director**

```sql
SELECT * FROM netflix WHERE director IS NULL;
```

### 13. **'Salman Khan' Movies in Last 10 Years**

```sql
SELECT * FROM netflix
WHERE casts ILIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

### 14. **Top 10 Indian Movie Actors**

```sql
SELECT UNNEST(STRING_TO_ARRAY(casts, ',')) AS actors,
       COUNT(*) AS total_content
FROM netflix
WHERE country ILIKE '%India%'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
```

### 15. **Content Categorized as 'Good' or 'Bad'**

```sql
WITH new_table AS (
    SELECT *,
           CASE WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad Content'
                ELSE 'Good Content' END AS category
    FROM netflix
)
SELECT category, COUNT(*) AS total_content
FROM new_table
GROUP BY 1;
```

*Insight:* Basic content flagging system based on description.

---

## 📊 Key Findings

* **Movies dominate** Netflix's catalog, but TV Shows still account for a significant portion.
* Most common ratings: **TV-MA** and **TV-14**, indicating content primarily targeted at mature audiences.
* **India and the US** are top producers of Netflix content.
* **Documentaries and dramas** are among the most popular genres.
* The keyword filter identified a subset of content labeled as **"Bad Content"** based on themes of violence or killing.

---

## 📄 File Structure

```
├── Netflix_Analysis.sql       # All SQL queries used in the analysis
├── netflix_titles.csv         # Raw data source from Kaggle
├── README.md                  # Documentation for the project
```

---

## 🚀 Future Enhancements

* Integrate SQL queries with Python using Pandas for enhanced data manipulation.
* Visualize key metrics with tools like Tableau or Power BI.
* Apply machine learning for predictive analytics (e.g., genre popularity, user behavior).

---

## 👨‍💼 Author

**Bhaskar Pal**
* Data enthusiast with a focus on analytics, SQL, and business intelligence.
📧 Contact: [bhaskarpal.official@gmail.com]  

---

## 📅 License

This project is licensed under the MIT License.
