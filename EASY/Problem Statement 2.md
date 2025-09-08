# 🎬 IMDb Weighted Rating Challenge  


<p align="center">
  <img src="https://media.giphy.com/media/3o7abKhOpu0NwenH3O/giphy.gif" width="300px" alt="Movie GIF">
</p>

---

## 📌 Problem Statement  2

Print the **genre** and the **maximum weighted rating** among all the movies of that genre released in **2014**.  

### 🔑 Rules  
1. Skip rows where **genre** or **weighted rating** is empty/null.  
2. Formula →  
   weighted_rating = (rating + metacritic/10.0) / 2
3. Output columns → `genre`, `weighted_rating`.
4. Sort genres **alphabetically**.

---

## 🎯 Expected Output

| genre     | weighted\_rating |
| --------- | ---------------- |
| Action    | 8.05             |
| Adventure | 8.45             |
| Animation | 8.05             |
| Biography | 7.85             |
| Comedy    | 8.45             |
| Crime     | 8.00             |
| Drama     | 8.95             |
| Horror    | 7.60             |
| Music     | 8.65             |
| Mystery   | 8.00             |
| Romance   | 7.45             |
| Sci-Fi    | 8.00             |
| Thriller  | 7.75             |
| War       | 7.00             |

---

## 💡 Solutions

### ✅ Solution 1 – Left Joins with Filters

```sql
SELECT genre,
       MAX((rating + metacritic/10.0)/2.0) AS weighted_rating
FROM earning
LEFT JOIN imdb ON earning.movie_id = imdb.movie_id AND title LIKE '%2014%'
LEFT JOIN genre ON earning.movie_id = genre.movie_id
WHERE genre IS NOT NULL
GROUP BY genre
HAVING MAX((rating + metacritic/10.0)/2.0) > 0
ORDER BY genre;
```

**Explanation (Step by Step):**

* Start with the **earning table**.
* Join it with **IMDb** data to get ratings & filter only **2014 movies**.
* Then join with **genre table** so we know the category of each movie.
* Calculate **weighted rating** using `(rating + metacritic/10)/2`.
* Use `MAX()` to pick the **highest weighted rating** per genre.
* Finally, show only valid genres and sort alphabetically.

---

### ✅ Solution 2 – Using Explicit Joins

```sql
SELECT g.genre,
       MAX((i.rating+i.metacritic/10.0)/2.0) AS weighted_rating
FROM imdb AS i
JOIN genre AS g ON i.movie_id = g.movie_id
WHERE i.title LIKE '%2014%'
  AND i.rating IS NOT NULL
  AND i.metacritic IS NOT NULL
  AND g.genre IS NOT NULL
GROUP BY g.genre
HAVING ROUND(AVG((i.rating+i.metacritic)/10),1) > 0
ORDER BY g.genre;
```

**Explanation:**

* Start from the **IMDb table** because it already has title & ratings.
* Join with **genre table** on `movie_id`.
* Filter → only **2014 movies**, and remove rows with **null ratings or null genres**.
* Calculate weighted rating & take the **max per genre**.
* Extra safety check: `HAVING AVG(...) > 0` ensures no invalid data.
* Order genres alphabetically.

---

### ✅ Solution 3 – Genre First

```sql
SELECT a.genre,
       MAX((b.rating+b.metacritic/10.0)/2.0) AS weighted_rating
FROM genre a
LEFT JOIN imdb b ON a.movie_id = b.movie_id
WHERE a.genre IS NOT NULL
  AND b.title LIKE '%2014%'
GROUP BY a.genre
ORDER BY a.genre;
```

**Explanation:**

* Start from **genre table** (every movie is linked with at least one genre).
* Bring IMDb data using a **left join**.
* Filter only 2014 movies.
* Compute weighted rating and find the **highest per genre**.
* Group by genre to get one row per genre.

---

### ✅ Solution 4 – IMDb First

```sql
SELECT b.genre,
       MAX((a.rating+a.metacritic/10.0)/2) AS weighted_rating
FROM imdb a
LEFT JOIN genre b ON a.movie_id = b.movie_id
WHERE a.title LIKE '%2014%'
  AND b.genre IS NOT NULL
GROUP BY b.genre
ORDER BY b.genre;
```

**Explanation:**

* Begin with **IMDb table** → because titles & ratings live here.
* Left join genres so we know what category the movie belongs to.
* Keep only **2014 movies** and remove null genres.
* Calculate max weighted rating for each genre.
* Sorted nicely by genre.

---

### ✅ Solution 5 – Using `USING()`

```sql
SELECT genre,
       MAX((rating + metacritic/10.0)/2) AS weighted_rating
FROM imdb AS i
JOIN genre AS g USING(movie_id)
WHERE i.title LIKE '%2014%'
  AND genre IS NOT NULL
  AND rating IS NOT NULL
  AND metacritic IS NOT NULL
GROUP BY genre
ORDER BY genre ASC;
```

**Explanation:**

* Instead of writing `ON i.movie_id = g.movie_id`, we use **`USING(movie_id)`** → shorter syntax.
* Same filtering: 2014 movies only, remove null values.
* Take the **maximum weighted rating** for each genre.
* Group by genre → one row per genre.
* Sort alphabetically.

---

### ✅ Solution 6 – Ranking Approach

```sql
WITH movie_wise_wavg AS (
   SELECT movie_id, title,
          (rating+metacritic/10.0)/2 AS weighted_rating
   FROM imdb
   WHERE title LIKE '%2014%'
   GROUP BY movie_id, title, (rating+metacritic/10.0)/2
   HAVING (rating+metacritic/10.0)/2 IS NOT NULL
),
rnk AS (
   SELECT g.genre, m.weighted_rating,
          RANK() OVER (PARTITION BY g.genre ORDER BY m.weighted_rating DESC) AS rnk
   FROM movie_wise_wavg m
   INNER JOIN genre g ON m.movie_id = g.movie_id
   WHERE g.genre IS NOT NULL
)
SELECT genre, weighted_rating
FROM rnk
WHERE rnk=1
ORDER BY genre;
```

**Explanation:**

* Step 1: Build a **CTE (movie\_wise\_wavg)** → calculate weighted rating for each movie in 2014.
* Step 2: In another CTE (**rnk**), give each movie a **rank** per genre based on its rating (highest first).
* Step 3: Pick only the **rank 1 movies** per genre (the top-rated one).
* Advantage: This method not only gets the top movie but can also be extended to show 2nd, 3rd best if needed.

---

### ✅ Solution 7 – Simple Inner Join

```sql
SELECT b.genre,
       MAX((c.rating + c.metacritic/10.0)/2) AS weighted_rating
FROM genre b
INNER JOIN imdb c ON c.movie_id = b.movie_id
WHERE c.title LIKE '%(2014)%'
  AND b.genre IS NOT NULL
  AND c.rating IS NOT NULL
  AND c.metacritic IS NOT NULL
GROUP BY b.genre
ORDER BY b.genre;
```

**Explanation:**

* Use an **inner join** → only rows that exist in both IMDb and genre tables will be included.
* Filter only **2014 movies**.
* Remove null values from genre, rating, and metacritic.
* Compute max weighted rating per genre.
* Order by genre alphabetically.

---

## 🏆 Key Takeaways

✨ Multiple ways to solve:

* `MAX()` → Quick and easy way to get top rating per genre.
* `RANK()` → More flexible, lets you see top-N movies per genre.
* Different join styles (LEFT, INNER, USING) → all work, just pick what feels cleanest.

💡 General Tip:

* Always filter early (`WHERE` clauses) to reduce data size.
* For performance, indexing `movie_id` and `title` is helpful.

---

<p align="center">
  <img src="https://media.giphy.com/media/xUOwG1XZUkwW2/giphy.gif" width="280px" alt="Data gif">
</p>

---

## 👤 Author

**Arjun Dixit (Arjundixit18)**
📌 Curating [SQLProblemVault](https://github.com/Arjundixit18/SQLProblemVault) 💾
🔥 Follow for **SQL | Data | DevOps | Projects**
