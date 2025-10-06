# ✨ SQL Challenge – Occupations Breakdown  

<p align="center">
  <img src="https://media.giphy.com/media/3oEjI6SIIHBdRxXI40/giphy.gif" alt="SQL Challenge">
</p>

## 📌 Problem Statement  

Generate the following **two result sets** from the `OCCUPATIONS` table:  

1️⃣ Query an **alphabetically ordered list** of all names in `OCCUPATIONS`, immediately followed by the **first letter of each profession in parentheses**.  
   > Example: `Ashely(P) Christeen(P) Jane(A) Jenny(D)`  

2️⃣ Query the **number of occurrences** of each occupation in `OCCUPATIONS`, sorted in ascending order by count and formatted as:  
   > There are a total of [count] [occupation]s.  

---

## 📥 Input Table (OCCUPATIONS)  

| Name       | Occupation |
|-------------|-------------|
| Samantha   | Doctor      |
| Julia      | Actor       |
| Maria      | Actor       |
| Meera      | Singer      |
| Ashely     | Professor   |
| Ketty      | Professor   |
| Christeen  | Professor   |
| Jane       | Actor       |
| Jenny      | Doctor      |
| Priya      | Singer      |

---

## 📤 Expected Output  


Ashely(P)
Christeen(P)
Jane(A)
Jenny(D)
Julia(A)
Ketty(P)
Maria(A)
Meera(S)
Priya(S)
Samantha(D)
There are a total of 2 doctors.
There are a total of 2 singers.
There are a total of 3 actors.
There are a total of 3 professors.



---

## ✅ Solutions  

---

### 🔹 Solution 1 – CONCAT + SUBSTRING  

```sql
SELECT CONCAT(Name, '(', SUBSTRING(Occupation, 1, 1), ')') 
FROM OCCUPATIONS
ORDER BY Name;

SELECT CONCAT('There are a total of ', COUNT(*), ' ', LOWER(Occupation), 's.')
FROM OCCUPATIONS
GROUP BY Occupation
ORDER BY COUNT(*) ASC, Occupation ASC;
````

* **TLDR**: Use `CONCAT` to combine names and initials; group by occupation for counts.
* **Approach**: Straightforward string manipulation and grouping.
* **Steps**:

  * Combine `Name` + `'(' + First letter of Occupation + ')'`.
  * Sort alphabetically by `Name`.
  * Count each `Occupation` and use `CONCAT` to format the output.
* **Key Observations**:

  * `SUBSTRING(Occupation, 1, 1)` extracts the first letter.
  * `LOWER()` ensures lowercase text in summary.
* **Complexity**: `O(N log N)` due to sorting and grouping.

---

### 🔹 Solution 2 – CASE WHEN for Initials

```sql
SELECT 
  CONCAT(Name, CASE 
    WHEN Occupation = 'Doctor' THEN '(D)'
    WHEN Occupation = 'Professor' THEN '(P)'
    WHEN Occupation = 'Singer' THEN '(S)'
    WHEN Occupation = 'Actor' THEN '(A)'
  END)
FROM OCCUPATIONS
ORDER BY Name;

SELECT 
  CONCAT('There are a total of ', COUNT(*), ' ', LOWER(Occupation), 's.')
FROM OCCUPATIONS
GROUP BY Occupation
ORDER BY COUNT(*) ASC, Occupation ASC;
```

* **TLDR**: Map occupations to initials using `CASE WHEN`.
* **Approach**: Explicitly define each initial per profession.
* **Steps**:

  * Use `CASE` to handle each occupation type.
  * Concatenate to form the output string.
  * Count and format summary lines.
* **Key Observations**:

  * Clear and readable for fixed occupation sets.
  * Ideal when initial rules aren’t consistent.
* **Complexity**: Slightly higher due to conditional checks; still `O(N log N)`.

---

### 🔹 Solution 3 – LEFT() Function Shortcut

```sql
SELECT CONCAT(Name, '(', LEFT(Occupation, 1), ')')
FROM OCCUPATIONS
ORDER BY Name;

SELECT CONCAT('There are a total of ', COUNT(*), ' ', LOWER(Occupation), 's.')
FROM OCCUPATIONS
GROUP BY Occupation
ORDER BY COUNT(*), Occupation;
```

* **TLDR**: Use `LEFT()` to grab the first character easily.
* **Approach**: Replace `SUBSTRING()` with `LEFT()` for simplicity.
* **Steps**:

  * Extract first letter with `LEFT(Occupation, 1)`.
  * Combine using `CONCAT`.
  * Count and format results.
* **Key Observations**:

  * Cleaner and shorter syntax.
  * Works identically across most SQL dialects.
* **Complexity**: `O(N log N)` similar to Solution 1.

---

### 🔹 Solution 4 – Subquery for Initials + Counts

```sql
SELECT CONCAT(Name, '(', (SELECT LEFT(OCCUPATION, 1)), ')') 
FROM OCCUPATIONS 
ORDER BY Name;

SELECT CONCAT('There are a total of ', occ_count, ' ', occ_name, 's.')
FROM (
  SELECT LOWER(Occupation) AS occ_name, COUNT(*) AS occ_count
  FROM OCCUPATIONS
  GROUP BY Occupation
) sub
ORDER BY occ_count, occ_name;
```

* **TLDR**: Use subquery to structure counts and reuse later.
* **Approach**: Perform grouping in a subquery, format outside.
* **Steps**:

  * Build a subquery that counts occupations.
  * Reference the subquery for final formatting.
* **Key Observations**:

  * Useful when chaining multiple aggregations.
  * Good for readability when adding filters.
* **Complexity**: `O(N log N)`; slight overhead due to subquery.

---

### 🔹 Solution 5 – Window Function Magic ✨

```sql
SELECT CONCAT(Name, '(', LEFT(Occupation, 1), ')')
FROM OCCUPATIONS
ORDER BY Name;

SELECT DISTINCT 
  CONCAT('There are a total of ', 
         COUNT(*) OVER (PARTITION BY Occupation), 
         ' ', LOWER(Occupation), 's.')
FROM OCCUPATIONS
ORDER BY COUNT(*) OVER (PARTITION BY Occupation), Occupation;
```

* **TLDR**: Use window functions for counts without grouping.
* **Approach**: Compute counts per occupation directly with `OVER()`.
* **Steps**:

  * Use `COUNT(*) OVER(PARTITION BY Occupation)` to get per-occupation totals.
  * Add `DISTINCT` to avoid repetition.
* **Key Observations**:

  * Eliminates need for `GROUP BY`.
  * Great for analytical queries.
* **Complexity**: `O(N log N)`; efficient for large datasets on modern SQL engines.

---

## 📝 Layman Explanation

Imagine a school 🏫 where every student writes their **name and profession** on the board.

1️⃣ First, we sort all names and write them with their **profession’s first letter**.

* *Samantha → Doctor → Samantha(D)*
* *Meera → Singer → Meera(S)*

2️⃣ Then, we count how many **Doctors**, **Singers**, **Actors**, and **Professors** there are —
and announce like a teacher 📢:

> “There are a total of 2 doctors.”

That’s literally what SQL is doing here — counting and formatting sentences.

---
<p align="center"> <img src="https://media.giphy.com/media/l0HlOvJ7yaacpuSas/giphy.gif" width="300"> <br> <em>Keep Querying. Keep Learning. 🚀</em> </p>


## 🎯 Final Thoughts

* ✅ **Simplest** → `LEFT()` or `SUBSTRING()` based solutions.
* ✅ **Readable** → `CASE WHEN` mapping.
* ✅ **Pro-Level** → Window function version.
* ✅ **Best for interviews** → Solution 1 (official HackerRank style).

---

<p align="center">
  <img src="https://media.giphy.com/media/U3qYN8S0j3bpK/giphy.gif" alt="SQL Fun" width="300">
</p>

