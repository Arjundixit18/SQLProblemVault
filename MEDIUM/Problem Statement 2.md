# ✨ SQL Challenge – Unique First Names

<p align="center">
  <img src="https://media.giphy.com/media/WoWm8YzFQJg5i/giphy.gif" alt="SQL Fun">
</p>


---

## 📌 Problem Statement  2

Show unique first names from the patients table which **only occur once** in the list.  

👉 If two or more people share the same name (e.g., *John*), do **NOT** include it.  
👉 If a name appears **only once** (e.g., *Leo*), include it in the result.  

---

## 📥 Input Table (patients)  

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

| first_name |
|------------|
| Abby       |
| Adelaide   |
| Adelia     |
| Akira      |
| Albert     |
| Aldo       |
| Alicia     |
| Allan      |
| Alpa       |
| Amane      |
| Amitabh    |
| Angel      |
| Anaplina   |

---

## ✅ Solutions  

---

### 🔹 Solution 1 – GROUP BY + HAVING  

```sql
SELECT first_name
FROM patients
GROUP BY first_name
HAVING COUNT(first_name) = 1;
````

* **TLDR**: Group names and keep only those with count = 1.
* **Approach**: Use `GROUP BY` + `HAVING`.
* **Steps**:

  * Group all rows by `first_name`.
  * Count how many times each name appears.
  * Keep only rows with count = 1.
* **Key Observations**:

  * Simple and very common SQL pattern.
  * `HAVING` works after grouping (like a filter for groups).
* **Complexity**: `O(N log N)` for grouping & counting.

---

### 🔹 Solution 2 – Subquery with Counts

```sql
SELECT first_name
FROM (
  SELECT first_name, COUNT(first_name) AS occurrencies
  FROM patients
  GROUP BY first_name
) sub
WHERE occurrencies = 1;
```

* **TLDR**: Count names in a subquery, filter outside.
* **Approach**: Break logic into two steps (count → filter).
* **Steps**:

  * Subquery: group by name, count occurrences.
  * Outer query: select only those where count = 1.
* **Key Observations**:

  * Useful if you want to reuse counts.
  * Cleaner when adding more filters later.
* **Complexity**: Same as solution 1, but slightly more overhead due to subquery.

---

### 🔹 Solution 3 – NOT IN with Duplicate Names

```sql
SELECT first_name
FROM patients
WHERE first_name NOT IN (
  SELECT first_name
  FROM patients
  GROUP BY first_name
  HAVING COUNT(*) > 1
);
```

* **TLDR**: Exclude all names that appear more than once.
* **Approach**: Use `NOT IN` with a subquery.
* **Steps**:

  * Find names that appear more than once.
  * Exclude them from the main query.
  * Return the rest.
* **Key Observations**:

  * Very intuitive “find duplicates then remove them.”
  * Works best with small datasets.
* **Complexity**: Can be slower for big tables (subquery executed).

---

### 🔹 Solution 4 – JOIN with Filtered Duplicates

```sql
SELECT p.first_name
FROM patients p
LEFT JOIN (
  SELECT first_name
  FROM patients
  GROUP BY first_name
  HAVING COUNT(*) > 1
) dup ON p.first_name = dup.first_name
WHERE dup.first_name IS NULL;
```

* **TLDR**: Use a join to remove duplicate names.
* **Approach**: Left join duplicates, then keep only rows without matches.
* **Steps**:

  * Build subquery for duplicate names.
  * Left join it with patients.
  * Keep only rows with `NULL` in duplicate side (i.e., no match).
* **Key Observations**:

  * Same logic as `NOT IN` but via joins.
  * Sometimes faster than `NOT IN`.
* **Complexity**: Similar to Solution 3.

---

### 🔹 Solution 5 – Window Function

```sql
SELECT first_name
FROM (
  SELECT first_name, COUNT(*) OVER(PARTITION BY first_name) AS cnt
  FROM patients
) t
WHERE cnt = 1;
```

* **TLDR**: Use window functions to count occurrences without grouping.
* **Approach**: Count each name's frequency using `OVER(PARTITION BY)`.
* **Steps**:

  * Apply window function partitioned by `first_name`.
  * Each row gets a count of its name’s occurrences.
  * Filter where count = 1.
* **Key Observations**:

  * No need for `GROUP BY`.
  * Handy in queries where you need both row-level + aggregate info.
* **Complexity**: Efficient on modern SQL engines, same order as grouping.

---

## 📝 Layman Explanation

Think of a classroom 🏫 full of students writing their names on the board.

* If **John** appears **3 times** → ignore.
* If **Leo** appears **once** → keep it.

We basically count how many times each name shows up, and keep only the “loners” 😎.

---

## 🎯 Final Thoughts

* ✅ **Most simple**: `GROUP BY + HAVING`.
* ✅ **Readable modular**: Subquery version.
* ✅ **Exclusion style**: `NOT IN` or `JOIN`.
* ✅ **Pro-level flex**: Window functions.

---

![Unique Names](https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExYjVpOTkxczh5cWVnaHllb2Jkb2FlYzY5MXMwcHZtNWw2cmI0ajhhZSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/fnDYk7bGCo7W4/giphy.gif)

