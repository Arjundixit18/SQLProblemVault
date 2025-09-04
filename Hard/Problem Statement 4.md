# 🏥 SQL Challenge – Generate Temporary Password for Patients

## 📌 Problem Statement 4
All patients who have gone through **admissions** are given a **temporary password** on their first admission.  

The password must be created in this order:
1. `patient_id`  
2. **Numerical length** of the patient’s `last_name`  
3. **Year** of the patient’s `birth_date`  

👉 Show the following:
- `patient_id`  
- `temp_password`

Tables to use: `patients`, `admissions`

---

## 🎯 Expected Output Example

| patient_id | temp_password |
|------------|---------------|
| 101        | 10131990      |
| 102        | 10271985      |
| 103        | 10351992      |

---

## 💡 Possible Solutions

### ✅ Solution 1 – Using `CONCAT` (SQL Server / MySQL)

```sql
SELECT DISTINCT
    P.patient_id,
    CONCAT(
        P.patient_id,
        LEN(P.last_name),
        YEAR(P.birth_date)
    ) AS temp_password
FROM patients P
JOIN admissions A ON A.patient_id = P.patient_id;
```

🔎 **Explanation**:
- `LEN(last_name)` → counts characters in the last name.  
- `YEAR(birth_date)` → extracts year from date of birth.  
- `CONCAT` → combines all into one password string.  
- `DISTINCT` → ensures each patient is shown once.

---

### ✅ Solution 2 – Using `||` Concatenation (Oracle / PostgreSQL style)

```sql
SELECT DISTINCT
    p.patient_id,
    p.patient_id || LENGTH(p.last_name) || EXTRACT(YEAR FROM p.birth_date) AS temp_password
FROM patients p
JOIN admissions a ON p.patient_id = a.patient_id;
```

🔎 **Explanation**:
- Uses `||` operator for string concatenation.  
- `LENGTH()` works instead of `LEN()`.  
- `EXTRACT(YEAR FROM …)` retrieves the year.

---

### ✅ Solution 3 – Group By to Ensure Unique Rows

```sql
SELECT
    pa.patient_id,
    pa.patient_id || LENGTH(pa.last_name) || EXTRACT(YEAR FROM pa.birth_date) AS temp_password
FROM patients pa
JOIN admissions ad ON pa.patient_id = ad.patient_id
GROUP BY pa.patient_id, pa.last_name, pa.birth_date;
```

🔎 **Explanation**:
- `GROUP BY` ensures uniqueness per patient even if multiple admissions exist.  
- Combines `patient_id`, name length, and birth year into one password.  

---

### ✅ Solution 4 – MySQL-Compatible Alternative

```sql
SELECT DISTINCT
    p.patient_id,
    CONCAT_WS('', p.patient_id, CHAR_LENGTH(p.last_name), YEAR(p.birth_date)) AS temp_password
FROM patients p
JOIN admissions a ON p.patient_id = a.patient_id;
```

🔎 **Explanation**:
- `CONCAT_WS('', …)` → concatenates values without a separator.  
- `CHAR_LENGTH()` is preferred in MySQL for character count.  
- Works even if `last_name` has multibyte characters.

---

## 🏆 Key Takeaways

* Password construction relies on **string concatenation + date functions**.  
* SQL syntax varies slightly:  
  - `LEN()` vs `LENGTH()` vs `CHAR_LENGTH()`  
  - `YEAR()` vs `EXTRACT(YEAR …)`  
  - `CONCAT()` vs `||` operator.  
* Always ensure **uniqueness** with `DISTINCT` or `GROUP BY` when joining admissions.  
* This type of question checks your knowledge of **string functions, date functions, and joins**.

🔥 Very common **SQL interview question** to test your ability to manipulate strings and dates.
