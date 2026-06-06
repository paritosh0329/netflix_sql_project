Netflix Movies and TV Shows Data Analysis using SQL
<img width="2226" height="678" alt="image" src="https://github.com/user-attachments/assets/05d6f16c-6f05-4236-97c8-422a27c6c241" />

# Netflix Movies and TV Shows Analysis Using SQL

## Project Overview

This project analyzes Netflix's Movies and TV Shows dataset using PostgreSQL. The objective is to answer real-world business questions, uncover content trends, and demonstrate SQL skills commonly required for Data Analyst roles.

The analysis includes data exploration, content distribution, ratings analysis, country-wise trends, genre analysis, and keyword-based content categorization.

---

## Objectives

* Analyze the distribution of Movies and TV Shows on Netflix.
* Identify the most common content ratings.
* Explore content based on release year, country, and duration.
* Discover trends in genres, actors, and directors.
* Perform business-driven analysis using SQL queries.
* Demonstrate practical SQL concepts including:

  * Aggregations
  * Window Functions
  * Common Table Expressions (CTEs)
  * String Functions
  * Date Functions
  * Data Cleaning Techniques

---

## Dataset

**Source:** Netflix Movies and TV Shows Dataset (Kaggle)

The dataset contains information about Netflix content, including:

* Content Type
* Title
* Director
* Cast
* Country
* Date Added
* Release Year
* Rating
* Duration
* Genre
* Description

---

## Database Schema

```sql
CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

---

## Business Problems and SQL Solutions

### 1. Count the Number of Movies vs TV Shows

**Objective:** Determine the distribution of content types on Netflix.

```sql
SELECT
    type,
    COUNT(*) AS total_content
FROM netflix
GROUP BY type;
```

---

### 2. Find the Most Common Rating for Movies and TV Shows

**Objective:** Identify the most frequently occurring rating for each content type.

```sql
WITH RatingCounts AS (
    SELECT
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT
        type,
        rating,
        rating_count,
        RANK() OVER (
            PARTITION BY type
            ORDER BY rating_count DESC
        ) AS rank
    FROM RatingCounts
)
SELECT
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
```

---

### 3. List All Movies Released in 2020

**Objective:** Retrieve all movies released in 2020.

```sql
SELECT *
FROM netflix
WHERE release_year = 2020
  AND type = 'Movie';
```

---

### 4. Top 5 Countries with the Most Content

**Objective:** Identify the countries contributing the highest amount of content.

```sql
SELECT *
FROM (
    SELECT
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
```

---

### 5. Identify the Longest Movie

**Objective:** Find the movie with the maximum duration.

```sql
SELECT *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC
LIMIT 1;
```

---

### 6. Find Content Added in the Last 5 Years

**Objective:** Analyze recently added content.

```sql
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY')
      >= CURRENT_DATE - INTERVAL '5 years';
```

---

### 7. Find All Movies/TV Shows by Rajiv Chilaka

**Objective:** Retrieve all content directed by Rajiv Chilaka.

```sql
SELECT *
FROM (
    SELECT *,
           UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';
```

---

### 8. List TV Shows with More Than 5 Seasons

**Objective:** Identify long-running TV shows.

```sql
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
```

---

### 9. Count Content by Genre

**Objective:** Analyze content distribution across genres.

```sql
SELECT
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY genre
ORDER BY total_content DESC;
```

---

### 10. Top 5 Years with Highest Content Releases in India

**Objective:** Analyze content release trends in India.

```sql
SELECT
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::NUMERIC /
        (SELECT COUNT(show_id)
         FROM netflix
         WHERE country = 'India')::NUMERIC * 100,
        2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
```

---

### 11. List All Documentary Movies

**Objective:** Retrieve documentary content.

```sql
SELECT *
FROM netflix
WHERE listed_in LIKE '%Documentaries';
```

---

### 12. Find Content Without a Director

**Objective:** Identify missing director information.

```sql
SELECT *
FROM netflix
WHERE director IS NULL;
```

---

### 13. Find Salman Khan Movies Released in the Last 10 Years

**Objective:** Analyze actor appearances.

```sql
SELECT *
FROM netflix
WHERE casts LIKE '%Salman Khan%'
AND release_year >
    EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

---

### 14. Top 10 Actors in Indian Content

**Objective:** Identify actors with the highest appearances.

```sql
SELECT
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*) AS total_movies
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY total_movies DESC
LIMIT 10;
```

---

### 15. Categorize Content Based on Keywords

**Objective:** Classify content using keywords.

```sql
SELECT
    category,
    COUNT(*) AS content_count
FROM (
    SELECT
        CASE
            WHEN description ILIKE '%kill%'
              OR description ILIKE '%violence%'
            THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;
```


Classify content as:

* **Bad** → Contains keywords such as "Kill" or "Violence"
* **Good** → Does not contain these keywords

---

## SQL Concepts Used

* SELECT Statements
* WHERE Clause
* GROUP BY
* ORDER BY
* Aggregate Functions
* CASE Statements
* Common Table Expressions (CTEs)
* Window Functions
* Date Functions
* String Functions
* Data Cleaning Techniques
* Data Transformation

---

## Key Insights

### Content Distribution

Netflix hosts a large collection of both Movies and TV Shows, with Movies forming the majority of the content.

### Ratings Analysis

Different content types have distinct rating distributions, helping identify target audiences.

### Country Analysis

A small number of countries contribute a significant portion of Netflix's content library.

### Genre Analysis

Certain genres dominate the platform, indicating viewer demand and content strategy.

### Indian Content Trends

Content production from India has increased significantly during specific years.

### Content Classification

Keyword-based analysis helps categorize content based on potentially sensitive themes.

---

## Project Deliverables

* PostgreSQL Database
* SQL Query Scripts
* Business Problem Statements
* Analytical Insights
* GitHub Documentation

---

## Skills Demonstrated

* SQL Query Writing
* Data Exploration
* Data Cleaning
* Data Analysis
* Business Intelligence
* Problem Solving
* Data Transformation
* PostgreSQL

---

## Conclusion

This project demonstrates how SQL can be used to transform raw data into actionable business insights. Through exploratory analysis and business-focused problem solving, the project highlights key content trends across Netflix's global catalog while strengthening practical SQL skills required for Data Analyst roles.

---

### Author

**Paritosh Vishal**
B.Sc. IT Graduate | Aspiring Data Analyst

**Skills:** SQL, Excel, Python, Power BI, Tableau, Data Analysis, Data Visualization

This project is part of my Data Analytics Portfolio and showcases practical SQL skills used in real-world business analysis.
