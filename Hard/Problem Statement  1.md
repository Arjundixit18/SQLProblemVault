Here’s a well-designed **Markdown file** you can use to showcase this hard SQL question with multiple solutions. I’ve made it visually **eye-catching** using headers, blockquotes, code blocks, tables, and highlights.

````markdown
# 🏥 SQL Challenge – Grouping Patients by Weight Range  

## 📌 Problem Statement  
Show all of the patients grouped into **weight groups**.  
- Each group should represent a range of 10 kg.  
- Example:  
  - If a patient weighs **100–109 kg**, they fall into the **100 weight group**.  
  - If a patient weighs **110–119 kg**, they fall into the **110 weight group**, and so on.  
- Display the **total number of patients** in each weight group.  
- Finally, **order the list by weight group in descending order**.

---

## 🎯 Expected Output Example  

| weight_group | patients_in_group |
|--------------|------------------|
| 120          | 3                |
| 110          | 6                |
| 100          | 2                |

---

## 💡 Possible Solutions  

### ✅ Solution 1 – Using Modulo `%`  
```sql
SELECT 
    COUNT(patient_id) AS total_patients,
    weight - weight % 10 AS weight_group
FROM patients
GROUP BY weight_group
ORDER BY weight_group DESC;
````

🔎 **Explanation**:

* `weight - weight % 10` rounds weight **down to the nearest 10**.
* Groups patients accordingly and counts them.

---

### ✅ Solution 2 – Using `TRUNCATE()`

```sql
SELECT 
    TRUNCATE(weight, -1) AS weight_group,
    COUNT(*) AS total_patients
FROM patients
GROUP BY weight_group
ORDER BY weight_group DESC;
```

🔎 **Explanation**:

* `TRUNCATE(weight, -1)` drops the last digit (e.g., 117 → 110).
* Groups and counts patients directly.

---

### ✅ Solution 3 – Using `FLOOR()`

```sql
SELECT 
    COUNT(*) AS patients_in_group,
    FLOOR(weight / 10) * 10 AS weight_group
FROM patients
GROUP BY weight_group
ORDER BY weight_group DESC;
```

🔎 **Explanation**:

* `FLOOR(weight / 10) * 10` ensures weights are **bucketed into multiples of 10**.
* Produces the same output with clear math.

---

## 🏆 Key Takeaways

* **Multiple SQL functions** (`%`, `TRUNCATE`, `FLOOR`) can solve the same problem.
* The choice depends on **database compatibility** and **readability**.
* Always **GROUP BY the transformed column**, not the raw `weight`.

🔥 This type of question is common in **SQL interviews** for testing your understanding of **grouping, rounding, and aggregations**.

---

✨ **Pro Tip**: Practice writing the same logic in **different SQL dialects** (MySQL, PostgreSQL, SQL Server, Oracle) to master cross-platform query writing.

```

Do you want me to also make this Markdown **GitHub-ready** with a **cover banner image + badges** (like "Difficulty: Hard" / "Topic: GROUP BY") so it looks even more professional in your portfolio?
```
