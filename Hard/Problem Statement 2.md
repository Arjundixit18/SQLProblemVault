

````markdown
# 🧮 SQL Challenge – Detecting Obesity from Patient Data  

## 📌 Problem Statement 2 
From the `patients` table, display the following:  
- **patient_id**  
- **weight (kg)**  
- **height (cm)**  
- **isObese** (as a boolean `0` or `1`)  

👉 A patient is considered **obese** if their **BMI** (Body Mass Index) is:  

\[
BMI = \frac{weight (kg)}{(height (m))^2} \geq 30
\]

⚠️ Note: Since height is stored in **cm**, convert it into **meters** before calculation.  

---

## 🎯 Expected Output Example  

| patient_id | weight | height | isObese |
|------------|--------|--------|---------|
| 101        | 95     | 170    | 1       |
| 102        | 60     | 175    | 0       |
| 103        | 110    | 165    | 1       |

---

## 💡 Possible Solutions  

### ✅ Solution 1 – Using `CASE` Expression  
```sql
SELECT 
    patient_id, 
    weight, 
    height,
    (CASE 
        WHEN weight / (POWER(height / 100.0, 2)) >= 30 THEN 1
        ELSE 0
     END) AS isObese
FROM patients;
````

🔎 **Layman Explanation**:

* Convert `height` from cm → meters using `/100.0`.
* Use **BMI formula**.
* If BMI ≥ 30 → return `1` (obese), else `0`.
* `CASE` is like an **if-else statement in SQL**.

---

### ✅ Solution 2 – Using Boolean Expression (Shortcut)

```sql
SELECT 
    patient_id,
    weight,
    height,
    (weight / ((height / 100.0) * (height / 100.0)) >= 30) AS isObese
FROM patients;
```

🔎 **Layman Explanation**:

* `(BMI >= 30)` directly returns **true (1)** or **false (0)**.
* No need for `CASE`.
* Cleaner but may look less readable to beginners.

---

### ✅ Solution 3 – Using `CAST()` for Precision

```sql
SELECT
    patient_id,
    weight,
    height,
    weight / POWER(CAST(height AS FLOAT) / 100, 2) >= 30 AS obese
FROM patients;
```

🔎 **Layman Explanation**:

* Explicitly converts `height` into **FLOAT** for accurate division.
* Ensures no accidental **integer division** problem (e.g., 170/100 = 1 instead of 1.7).

---

### ✅ Solution 4 – The Cleanest `POWER()` Formula

```sql
SELECT
    patient_id,
    weight,
    height,
    weight / POWER(height / 100.0, 2) >= 30 AS obese
FROM patients;
```

🔎 **Layman Explanation**:

* Same as Solution 1 but **without CASE**.
* Directly outputs `1` (true) or `0` (false).
* Clean, short, and works in most SQL dialects.

---

## 🏆 Key Takeaways

* Always convert **height (cm → m)** before BMI calculation.
* Use `/100.0` (with decimal) to force **floating-point math**.
* SQL allows multiple ways to solve the same problem:

  * `CASE WHEN` (explicit control)
  * Boolean expressions (compact)
  * `CAST()` for **type safety**
* Pick the style that balances **readability** + **efficiency**.

🔥 This is a common **SQL health dataset challenge**—perfect for interviews and real-world analytics.

---

✨ **Pro Tip**: If your DBMS doesn’t support boolean `0/1` directly, wrap it with `CASE` for clarity.
