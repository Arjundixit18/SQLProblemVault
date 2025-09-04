# 🏥 SQL Problem – Provinces with More Male Patients than Female

## 📌 Problem Statement  
Show the **provinces** that have **more patients identified as 'M' than 'F'**.  
- Only show the **full `province_name`**.  
- Use the tables: `patients`, `province_names`.

---

## 🎯 Expected Output  
Example (based on dataset):

| province_name              |
|----------------------------|
| Alberta                    |
| British Columbia           |
| Ontario                    |

---

## 💡 Possible Solutions

### ✅ Solution 1 – Using `COUNT(CASE WHEN …)`  
```sql
SELECT pr.province_name
FROM patients AS pa
JOIN province_names AS pr 
  ON pa.province_id = pr.province_id
GROUP BY pr.province_name
HAVING COUNT(CASE WHEN gender = 'M' THEN 1 END) >
       COUNT(CASE WHEN gender = 'F' THEN 1 END);
```


🔎 **Explanation**

* `COUNT(CASE WHEN gender = 'M' THEN 1 END)` counts rows where gender = 'M'.
* Likewise for `'F'`.
* `HAVING` compares those aggregated counts per `province_name`.
* Good for clarity and portable across DBMSs.

---

### ✅ Solution 2 – Derived Table with `SUM` booleans

```sql
SELECT province_name
FROM (
    SELECT province_name,
           SUM(gender = 'M') AS n_male,
           SUM(gender = 'F') AS n_female
    FROM patients pa
    JOIN province_names pr 
      ON pa.province_id = pr.province_id
    GROUP BY province_name
) sub
WHERE n_male > n_female;
```

🔎 **Explanation**

* The subquery aggregates counts per `province_name`.
* Many SQL dialects treat boolean expressions as `1` (true) or `0` (false), so `SUM(gender = 'M')` counts males.
* Outer query simply filters where male count > female count.
* Nice separation of aggregation and filtering.

---

### ✅ Solution 3 – `SUM` Shortcut in `HAVING`

```sql
SELECT pr.province_name
FROM patients AS pa
JOIN province_names AS pr 
  ON pa.province_id = pr.province_id
GROUP BY pr.province_name
HAVING SUM(gender = 'M') > SUM(gender = 'F');
```

🔎 **Explanation**

* Same idea as Solution 2 but done directly in `HAVING`.
* `SUM(gender = 'M')` and `SUM(gender = 'F')` do boolean counting inline.
* Shorter and very readable where boolean-to-int conversion is supported.

---

### ✅ Solution 4 – Majority (over 50%) test

```sql
SELECT pr.province_name
FROM patients AS pa
JOIN province_names AS pr 
  ON pa.province_id = pr.province_id
GROUP BY pr.province_name
HAVING COUNT(CASE WHEN gender = 'M' THEN 1 END) > COUNT(*) * 0.5;
```

🔎 **Explanation**

* `COUNT(*) * 0.5` computes half of all patients in the province.
* If male count > 50% of all patients, the province qualifies.
* This is slightly stricter than `male > female` when there are NULL genders — it ignores NULLs in the male count but includes them in `COUNT(*)`. Use carefully if NULLs exist.

---

### ✅ Solution 5 – `IN` with aggregated `province_id` subquery

```sql
SELECT province_name 
FROM province_names
WHERE province_id IN (
    SELECT province_id
    FROM patients
    GROUP BY province_id
    HAVING SUM(gender = 'M') > SUM(gender = 'F')
);
```

🔎 **Explanation**

* Inner subquery finds `province_id`s where male > female.
* Outer query maps those IDs to full `province_name`.
* Useful if you want to work with IDs first (e.g., when joining many lookup tables separately).

---

### ✅ Solution 6 – +1 / -1 trick for compact comparison

```sql
SELECT province_name
FROM patients p
JOIN province_names r 
  ON p.province_id = r.province_id
GROUP BY province_name
HAVING SUM(CASE WHEN gender = 'M' THEN 1 ELSE -1 END) > 0;
```

🔎 **Explanation**

* Adds `+1` for each male and `-1` for each female; the sum is positive when males outnumber females.
* Compact and fast (fewer aggregates), but requires careful handling if there are NULLs — NULLs will be treated as `ELSE -1` here, which may be undesirable. Consider `ELSE 0` if you want to ignore unknown gender.

---

### ✅ Solution 7 – Explicit `CASE` counting (very clear)

```sql
SELECT pn.province_name
FROM province_names pn
JOIN patients p 
  ON pn.province_id = p.province_id
GROUP BY pn.province_name
HAVING SUM(CASE WHEN p.gender = 'M' THEN 1 ELSE 0 END) >
       SUM(CASE WHEN p.gender = 'F' THEN 1 ELSE 0 END);
```

🔎 **Explanation**

* Uses `CASE ... THEN 1 ELSE 0 END` which is explicit and DBMS-portable.
* Avoids reliance on boolean-to-int casting.
* Good for clarity and to avoid surprises with NULLs.

---

### ✅ Solution 8 – Clean CTE (readable, maintainable)

```sql
WITH gender_count AS (
    SELECT pr.province_name,
           SUM(CASE WHEN pa.gender = 'M' THEN 1 ELSE 0 END) AS males,
           SUM(CASE WHEN pa.gender = 'F' THEN 1 ELSE 0 END) AS females
    FROM patients pa
    JOIN province_names pr 
      ON pa.province_id = pr.province_id
    GROUP BY pr.province_name
)
SELECT province_name
FROM gender_count
WHERE males > females;
```

🔎 **Explanation**

* CTE performs aggregation with explicit `CASE` counts.
* Final `SELECT` filters the aggregated results.
* Great for readability and incremental debugging.

---

### ✅ Solution 9 – Join with pre-aggregated counts

```sql
SELECT DISTINCT pr.province_name
FROM patients pa
JOIN province_names pr 
  ON pa.province_id = pr.province_id
JOIN (
    SELECT province_id,
           SUM(gender = 'M') AS male_count,
           SUM(gender = 'F') AS female_count
    FROM patients
    GROUP BY province_id
) counts ON counts.province_id = pa.province_id
WHERE counts.male_count > counts.female_count;
```

🔎 **Explanation**

* An aggregated inline table (`counts`) computes male/female counts by `province_id`.
* Main query joins that back to get `province_name` and filters where males > females.
* Useful when you want counts available for further logic or columns.

---

## 📌 Key Takeaways

* You can count genders with `COUNT(CASE WHEN ...)`, `SUM(CASE WHEN ...)`, or by leveraging boolean-to-int conversions (`SUM(gender = 'M')`).
* Use explicit `CASE ... ELSE 0` for portability and to avoid NULL surprises.
* Choose CTEs or subqueries for readability and maintainability on complex queries.
* Watch out for NULL `gender` values and pick `ELSE 0` if you need to ignore them.

Which of those would you like next?
