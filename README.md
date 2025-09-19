# 🔥 SQLProblemVault  


> A curated collection of **SQL challenges** with clean, optimized solutions — perfect for 💼 interviews, 📖 practice, and 🧠 mastering query logic.  

<p align="center">
  <img src="https://media.giphy.com/media/f9k1tV7HyORcngKF8v/giphy.gif" width="350px" alt="SQL GIF">
</p>


---

## ✨ Why This Repo?  
💡 Because boring SQL notes are out.  
This vault is designed for:  
- 🎯 **Interview Prep** – Nail your next data/analytics/engineering interview  
- 🧩 **Problem-Solving** – Learn multiple ways to crack the same problem  
- 🧑‍💻 **Practice** – Improve by hands-on coding, not just theory  
- 🏆 **Level Up** – Easy → Medium → Hard SQL challenges  

---

## 🗂️ Problem Levels  

| Difficulty | Badge |
|------------|-------|
| 🟢 Easy     | ![Easy](https://img.shields.io/badge/SQL-Easy-brightgreen?style=flat-square&logo=databricks) |
| 🟡 Medium   | ![Medium](https://img.shields.io/badge/SQL-Medium-yellow?style=flat-square&logo=postgresql) |
| 🔴 Hard     | ![Hard](https://img.shields.io/badge/SQL-Hard-red?style=flat-square&logo=mysql) |

---

## 📚 Table of Contents  

1. 🏥 [Problem 1 – Temporary Passwords for Patients](./Problem%20Statement%201.md)  
2. 💳 [Problem 2 – Insurance Cost Calculation](./Problem%20Statement%202.md)  
3. 🌍 [Problem 3 – Provinces with More Male Patients](./Problem%20Statement%203.md)  
4. 🧑‍⚕️ [Problem 4 – Patients Attended by Doctor Lisa](./Problem%20Statement%204.md)  
5. 📊 [Problem 5 – Weight Grouping of Patients](./Problem%20Statement%205.md)  
6. 🔒 [Problem 6 – 🚀 Coming Soon!](./Problem%20Statement%206.md)  

---

## 🎨 Sample Problem (Sneak Peek)  

### 💡 Problem 2 – Insurance Cost Calculation  


```sql
SELECT 
  CASE 
    WHEN patient_id % 2 = 0 THEN 'Yes'
    ELSE 'No'
  END AS has_insurance,
  SUM(
    CASE 
      WHEN patient_id % 2 = 0 THEN 10
      ELSE 50
    END
  ) AS admission_total
FROM admissions
GROUP BY has_insurance;
```

✅ Calculates patient insurance status (`Yes/No`)
✅ Aggregates **total admission costs** by group

---

## 🚀 Contribution Guide

Want to add your SQL magic?

1. 🍴 Fork this repo
2. 🌱 Create a branch `feature/new-problem`
3. 🛠️ Add your problem + solution in `Problem Statement X.md` format
4. 📩 Submit a PR

We love **clean, readable queries** with multiple solutions!

---

## 📌 Connect with Me

👤 **Arjun Dixit**
🔗 [GitHub – Arjundixit18](https://github.com/Arjundixit18)
💼 Open to collabs, projects & discussions around **SQL | Data | DevOps**

<p align="center">
  <img src="https://media.giphy.com/media/W3Chvlt6Z8MKv5WAFs/giphy.gif" width="300px" alt="SQL Cool GIF">
</p>

---

## ⭐ Support

If this repo helps you:

* 🌟 **Star it** (so more devs find it)
* 🐦 Share with your friends
* 💬 Drop suggestions in **Issues**

> “SQL isn’t just about queries. It’s about asking the right questions.”

---
