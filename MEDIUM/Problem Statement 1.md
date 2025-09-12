# 🗓️ SQL Challenge — Show Unique Birth Years from Patients  

> **File:** `Problem1_UniqueBirthYears.md`  

---

<div align="center">
  <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExNzJvMTgybmRoYWdvcTBqNHRlNWtwaW5tZXJxN3NobWNjMHRyZnBuNCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/f6pOe5e2r8Fz1wKjWi/giphy.gif" width="420" alt="sql gif"/>
</div>

---

## 📌 Problem Statement 1 

We need to:  
- Find **all birth years** from the `patients` table.  
- Make them **unique** (no duplicates).  
- Sort them in **ascending order**.  

---


## 📥 Input (patients table)

| patient_id | first_name | last_name  | gender | birth_date | city     | province_id | allergies   | height | weight |
|------------|------------|------------|--------|------------|----------|-------------|-------------|--------|--------|
| 1          | Donald     | Waterfield | M      | 1963-02-12 | Barrie   | ON          | NULL        | 156    | 65     |
| 2          | Mickey     | Baasha     | M      | 1981-05-28 | Dundas   | ON          | Sulfa       | 185    | 76     |
| 3          | Jiji       | Sharma     | M      | 1957-09-05 | Hamilton | ON          | Penicillin  | 194    | 106    |
| 4          | Blair      | Diaz       | M      | 1967-01-07 | Hamilton | ON          | NULL        | 191    | 104    |
| 5          | Charles    | Wolfe      | M      | 2017-11-19 | Orillia  | ON          | Penicillin  | 47     | 10     |
| 6          | Sue        | Falcon     | F      | 2017-09-30 | Ajax     | ON          | Penicillin  | 43     | 5      |

---

## 📤 Expected Output  

| birth_year |
|------------|
| 1918       |
| 1923       |
| 1925       |
| 1926       |
| 1927       |
| 1928       |
| 1929       |
| 1931       |
| 1933       |
| 1934       |
| 1936       |
| 1937       |
| 1938       |
| 1939       |
| 1940       |
| 1941       |
| 1942       |
| 1943       |
| 1944       |
| 1945       |
| 1946       |

---

## ✅ Solutions  

---

### 💡 Solution 1 — Using DISTINCT  
```sql
SELECT DISTINCT YEAR(birth_date) AS birth_year
FROM patients
ORDER BY birth_year;
````

* **TLDR**

  * Use `DISTINCT` to remove duplicates and sort the years.

* **Approach**

  * Apply `YEAR()` on `birth_date`, keep only unique values using `DISTINCT`.

* **Steps**

  1. Extract year from `birth_date`.
  2. Use `DISTINCT` to get unique values.
  3. ORDER BY year in ascending order.

* **Key Observations**

  * `DISTINCT` works well when you only need unique values.
  * Simple, clean, beginner-friendly.

* **Complexity & Explanation**

  * **Time:** O(N log N) (due to sort).
  * **Space:** O(N) (for unique year set).
  * Super straightforward and easy to read.

---

### 💡 Solution 2 — Using GROUP BY

```sql
SELECT YEAR(birth_date) AS birth_year
FROM patients
GROUP BY YEAR(birth_date)
ORDER BY birth_year;
```

* **TLDR**

  * Use `GROUP BY` on year to get unique values.

* **Approach**

  * Instead of `DISTINCT`, group records by year.

* **Steps**

  1. Extract year from `birth_date`.
  2. GROUP BY that year.
  3. ORDER BY year ascending.

* **Key Observations**

  * `DISTINCT` and `GROUP BY` both give unique values.
  * This method is useful if you later want aggregate info (like count per year).

* **Complexity & Explanation**

  * **Time:** O(N log N) due to sorting + grouping.
  * **Space:** O(N) for group storage.
  * More flexible than DISTINCT if we expand query later.

---

### 💡 Solution 3 — Using a Subquery

```sql
SELECT birth_year
FROM (
  SELECT YEAR(birth_date) AS birth_year
  FROM patients
) AS t
GROUP BY birth_year
ORDER BY birth_year;
```

* **TLDR**

  * First extract years, then group them outside.

* **Approach**

  * Use a subquery for clarity: extract all years, then group by outer query.

* **Steps**

  1. Create subquery: extract year from each patient.
  2. GROUP BY birth\_year in outer query.
  3. Sort ascending.

* **Key Observations**

  * Slightly overkill for this problem, but improves readability in complex queries.
  * Flexible if we later add more joins or filters.

* **Complexity & Explanation**

  * **Time:** O(N log N). **Space:** O(N).
  * Same as Sol 2, just with more structure.

---

### 💡 Solution 4 — Using CTE (Common Table Expression)

```sql
WITH years AS (
  SELECT YEAR(birth_date) AS birth_year
  FROM patients
)
SELECT birth_year
FROM years
GROUP BY birth_year
ORDER BY birth_year;
```

* **TLDR**

  * Use a CTE to separate logic into steps.

* **Approach**

  * Extract years in a CTE, then group and sort in the main query.

* **Steps**

  1. Build CTE `years` with `YEAR(birth_date)`.
  2. Select from that CTE.
  3. GROUP BY and ORDER BY.

* **Key Observations**

  * Makes queries more modular and readable.
  * Handy when queries get very large.

* **Complexity & Explanation**

  * **Time:** O(N log N). **Space:** O(N).
  * Great for readability, identical result.

---

### 💡 Solution 5 — Using Window Function (extra style)

```sql
SELECT DISTINCT birth_year
FROM (
  SELECT YEAR(birth_date) AS birth_year,
         ROW_NUMBER() OVER(PARTITION BY YEAR(birth_date) ORDER BY YEAR(birth_date)) as rn
  FROM patients
) t
WHERE rn = 1
ORDER BY birth_year;
```

* **TLDR**

  * Use `ROW_NUMBER()` to keep one row per unique year.

* **Approach**

  * Assign row numbers partitioned by year, pick only the first row.

* **Steps**

  1. Extract year and assign row numbers.
  2. Partition ensures rows are grouped by year.
  3. Keep `rn=1`.
  4. Sort ascending.

* **Key Observations**

  * Over-engineered for this task, but shows window functions usage.
  * Useful if you later want first patient per year.

* **Complexity & Explanation**

  * **Time:** O(N log N). **Space:** O(N).
  * Window function adds slight overhead vs DISTINCT/GROUP BY.

---

##  Final Takeaways

* **Best choices:** Solution 1 (DISTINCT) or Solution 2 (GROUP BY).
* **Readable / expandable:** Solution 4 (CTE).
* **Overkill but educational:** Solution 5 (Window Functions).

---

<div align="center">
  <img src="https://media.giphy.com/media/l41lVsYDBC0UVQJCE/giphy.gif" width="380" alt="clean sql"/>
  <p><small>SQL can be this clean & fun 🚀</small></p>
</div>
  

