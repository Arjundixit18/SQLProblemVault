# 🏥 SQL Challenge – Insurance Status & Admission Costs

## 📌 Problem Statement 5
Each admission costs:  
- **$50** for patients **without insurance**  
- **$10** for patients **with insurance**  

👉 Rule:  
- All patients with an **even `patient_id` have insurance**.  
- Patients with an **odd `patient_id` do not**.  

Show:  
1. `has_insurance` → `'Yes'` if insured, `'No'` otherwise  
2. Total `admission_total` cost for each group  

Tables: `patients`, `admissions`

---

## 🎯 Expected Output Example

| has_insurance | admission_total |
|---------------|-----------------|
| Yes           | 120             |
| No            | 350             |

---

## 💡 Possible Solutions

### ✅ Solution 1 – Derived Table + CASE
```sql
SELECT has_insurance, SUM(admission_cost) AS admission_total
FROM (
    SELECT 
        patient_id,
        CASE WHEN patient_id % 2 = 0 THEN 'Yes' ELSE 'No' END AS has_insurance,
        CASE WHEN patient_id % 2 = 0 THEN 10 ELSE 50 END AS admission_cost
    FROM admissions
) t
GROUP BY has_insurance;
```

🔎 **Explanation**:  
- Build a temp table with `has_insurance` + `admission_cost`.  
- Aggregate by insurance status.  

---

### ✅ Solution 2 – Precompute Insurance Then Multiply
```sql
SELECT
    has_insurance,
    CASE 
        WHEN has_insurance = 'Yes' THEN COUNT(*) * 10
        ELSE COUNT(*) * 50
    END AS cost_after_insurance
FROM (
    SELECT 
        CASE WHEN patient_id % 2 = 0 THEN 'Yes' ELSE 'No' END AS has_insurance
    FROM admissions
) sub
GROUP BY has_insurance;
```

🔎 **Explanation**:  
- Only calculate insurance status first.  
- Then apply cost per admission via multiplication.  

---

### ✅ Solution 3 – Direct Grouping with CASE
```sql
SELECT 
    CASE WHEN patient_id % 2 = 0 THEN 'Yes' ELSE 'No' END AS has_insurance,
    SUM(
        CASE WHEN patient_id % 2 = 0 THEN 10 ELSE 50 END
    ) AS admission_total
FROM admissions
GROUP BY has_insurance;
```

🔎 **Explanation**:  
- Compute both insurance status & cost inline.  
- Cleaner than derived subquery.  

---

### ✅ Solution 4 – Using `UNION`
```sql
SELECT 'No' AS has_insurance, COUNT(*) * 50 AS cost
FROM admissions
WHERE patient_id % 2 = 1
GROUP BY has_insurance
UNION
SELECT 'Yes' AS has_insurance, COUNT(*) * 10 AS cost
FROM admissions
WHERE patient_id % 2 = 0
GROUP BY has_insurance;
```

🔎 **Explanation**:  
- Split insured vs uninsured into **two queries**, then `UNION`.  
- Works but less elegant.  

---

### ✅ Solution 5 – Joining Patients + Admissions
```sql
SELECT 
    CASE WHEN MOD(p.patient_id, 2) = 0 THEN 'Yes' ELSE 'No' END AS has_insurance,
    COUNT(a.admission_id) AS total_admissions,
    SUM(
        CASE WHEN MOD(p.patient_id, 2) = 0 THEN 10 ELSE 50 END
    ) AS admission_total
FROM patients p
JOIN admissions a ON p.patient_id = a.patient_id
GROUP BY has_insurance;
```

🔎 **Explanation**:  
- Explicitly joins patients and admissions.  
- Counts admissions + calculates cost.  

---

### ✅ Solution 6 – My Easy Variations

**Option A – Using `IIF()` (SQL Server style):**
```sql
SELECT 
    IIF(patient_id % 2 = 0, 'Yes', 'No') AS has_insurance,
    SUM(IIF(patient_id % 2 = 0, 10, 50)) AS admission_total
FROM admissions
GROUP BY IIF(patient_id % 2 = 0, 'Yes', 'No');
```

**Option B – Using `CASE` but Simpler:**
```sql
SELECT 
    CASE patient_id % 2 WHEN 0 THEN 'Yes' ELSE 'No' END AS has_insurance,
    SUM(CASE patient_id % 2 WHEN 0 THEN 10 ELSE 50 END) AS admission_total
FROM admissions
GROUP BY CASE patient_id % 2 WHEN 0 THEN 'Yes' ELSE 'No' END;
```

🔎 **Explanation**:  
- `IIF()` is shorthand for `CASE` (works in SQL Server / MS Access).  
- Both queries reduce repetition by reusing the same condition in `GROUP BY`.  

---

## 🏆 Key Takeaways
* **Modulus operator `%` or `MOD()`** is perfect for even/odd logic.  
* Multiple approaches possible:  
  - Derived subquery  
  - Direct inline CASE  
  - UNION splitting groups  
  - Join with patients  
* Always **match SQL dialect**:  
  - `IIF()` works in SQL Server, not in MySQL.  
  - `LEN()` vs `LENGTH()` differences too.  
* This type of question checks **CASE usage, grouping, and arithmetic in SQL**.  

🔥 Great interview-style problem for practicing **conditional aggregation**.  
