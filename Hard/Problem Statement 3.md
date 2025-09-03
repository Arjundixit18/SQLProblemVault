

````markdown
# 🏥 SQL Challenge – Patients with Epilepsy and Doctor Lisa

## 📌 Problem Statement 3
Show the following details:  
- **patient_id**  
- **first_name**  
- **last_name**  
- **attending doctor's specialty**  

👉 Only include patients who:  
- Have a diagnosis of **'Epilepsy'**  
- Are attended by a doctor whose **first_name = 'Lisa'**  

Use the tables: `patients`, `admissions`, and `doctors`.

---

## 🎯 Expected Output Example  

| patient_id | first_name | last_name | attending_doctor_specialty |
|------------|------------|-----------|-----------------------------|
| 101        | John       | Doe       | Neurology                   |
| 102        | Alice      | Smith     | Pediatrics                  |

---

## 💡 Possible Solutions  


````

### ✅ Solution 1 – Simple `JOIN`s  

```sql
SELECT
    p.patient_id,
    p.first_name AS patient_first_name,
    p.last_name AS patient_last_name,
    ph.specialty AS attending_doctor_specialty
FROM patients p
JOIN admissions a ON a.patient_id = p.patient_id
JOIN doctors ph ON ph.doctor_id = a.attending_doctor_id
WHERE ph.first_name = 'Lisa'
  AND a.diagnosis = 'Epilepsy';
```

🔎 **Explanation**:

* Join all three tables on their keys.
* Filter only records where diagnosis = **Epilepsy** and doctor’s first\_name = **Lisa**.

---

### ✅ Solution 2 – Subquery Join with `USING`

```sql
SELECT
    pa.patient_id,
    pa.first_name,
    pa.last_name,
    ph1.specialty
FROM patients AS pa
JOIN (
    SELECT *
    FROM admissions AS a
    JOIN doctors AS ph ON a.attending_doctor_id = ph.doctor_id
) AS ph1 USING (patient_id)
WHERE ph1.diagnosis = 'Epilepsy'
  AND ph1.first_name = 'Lisa';
```

🔎 **Explanation**:

* Inner join between **admissions** and **doctors** first.
* Then join with **patients** using `USING(patient_id)`.
* Apply filters afterward.

---

### ✅ Solution 3 – Old-School `FROM` with Multiple Tables

```sql
SELECT
    a.patient_id,
    a.first_name,
    a.last_name,
    b.specialty
FROM patients a, doctors b, admissions c
WHERE a.patient_id = c.patient_id
  AND c.attending_doctor_id = b.doctor_id
  AND c.diagnosis = 'Epilepsy'
  AND b.first_name = 'Lisa';
```

🔎 **Explanation**:

* Uses the older **comma-style join** (not recommended in modern SQL).
* Still valid but less readable than explicit JOINs.

---

### ✅ Solution 4 – Using a CTE for Patients with Epilepsy

```sql
WITH patient_table AS (
    SELECT
        patients.patient_id,
        patients.first_name,
        patients.last_name,
        admissions.attending_doctor_id
    FROM patients
    JOIN admissions ON patients.patient_id = admissions.patient_id
    WHERE admissions.diagnosis = 'Epilepsy'
)
SELECT
    patient_table.patient_id,
    patient_table.first_name,
    patient_table.last_name,
    doctors.specialty
FROM patient_table
JOIN doctors ON patient_table.attending_doctor_id = doctors.doctor_id
WHERE doctors.first_name = 'Lisa';
```

🔎 **Explanation**:

* First filter epilepsy patients inside a **CTE**.
* Then join with **doctors** to get only Lisa’s patients.
* More structured and readable.

---

### ✅ Solution 5 – Two-Step CTE for Clarity

```sql
WITH epilepsy_admissions AS (
    SELECT 
        patient_id,
        attending_doctor_id
    FROM admissions
    WHERE diagnosis = 'Epilepsy'
)
SELECT 
    p.patient_id,
    p.first_name,
    p.last_name,
    d.specialty
FROM epilepsy_admissions ea
JOIN patients p ON ea.patient_id = p.patient_id
JOIN doctors d ON ea.attending_doctor_id = d.doctor_id
WHERE d.first_name = 'Lisa';
```

🔎 **Explanation**:

* Step 1: Select only **epilepsy-related admissions**.
* Step 2: Join with patients and doctors to get names + specialty.
* Very clean and modular query.

---

## 🏆 Key Takeaways

* Same problem can be solved with **plain JOINs, subqueries, old-style joins, or CTEs**.
* **CTEs (Solutions 4 & 5)** make the logic cleaner and easier to maintain.
* Always filter early when possible (like diagnosis = 'Epilepsy') to reduce result size.
* Use **modern explicit JOIN syntax** instead of comma joins for readability.

🔥 This question tests your knowledge of **multi-table joins**, **filtering**, and **query structuring**.
