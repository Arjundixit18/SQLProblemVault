# 👨‍🦱 SQL Challenge – Male Patients Only  

> 🎯 Goal: Retrieve details of **patients whose gender is 'M'**.  

<p align="center">
  <img src="https://media.giphy.com/media/13HgwGsXF0aiGY/giphy.gif" width="300px" alt="SQL cool gif">
</p>

---

## 📌 Problem Statement  
Show the following details for patients:  
- **first_name**  
- **last_name**  
- **gender**  

👉 Only include patients where `gender = 'M'`.  

---

## 🎯 Expected Output Example  

| first_name | last_name | gender |
|------------|-----------|--------|
| John       | Doe       | M      |
| Alex       | Carter    | M      |
| Raj        | Kumar     | M      |

---

## 💡 Possible Solutions  

### ✅ Solution 1 – Straightforward `WHERE`  

```sql
SELECT
  first_name,
  last_name,
  gender
FROM patients
WHERE gender = 'M';
```

🔎 **Explanation**:

* Simple filtering using the `WHERE` clause.
* Best for readability and performance.

---

### ✅ Solution 2 – Using `CASE` Filter

```sql
SELECT
  first_name,
  last_name,
  gender
FROM patients
WHERE CASE WHEN gender = 'M' THEN 1 ELSE 0 END = 1;
```

🔎 **Explanation**:

* Uses a `CASE` expression inside the `WHERE` clause.
* Not the shortest, but shows how conditions can be transformed into Boolean checks.

---

### ✅ Solution 3 – Pattern Matching with `LIKE`

```sql
SELECT
  first_name,
  last_name,
  gender
FROM patients
WHERE gender LIKE 'M';
```

🔎 **Explanation**:

* Uses `LIKE` to match the string `'M'`.
* Functionally same as `=` here, but shows flexibility for scenarios with partial matches.

---

## 🏆 Key Takeaways

✨ Multiple ways to solve the same SQL problem:

* Use **`=`** for direct checks (fastest & cleanest).
* Use **`CASE`** when transforming logic.
* Use **`LIKE`** when pattern-matching is needed.

🔥 This is an **Easy-Level SQL Problem** but teaches you to think about **different query styles**.

---

<p align="center">
  <img src="https://media.giphy.com/media/xT0GqeSlGSRQut4RU8/giphy.gif" width="280px" alt="SQL fun gif">
</p>

---

## 👤 Author

**Arjun Dixit (Arjundixit18)**
📌 Curating [SQLProblemVault](https://github.com/Arjundixit18/SQLProblemVault) 💾
💡 Follow for **SQL | Data | DevOps | Projects**

---
