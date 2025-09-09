# Employee Bonus

> **File:** `EmployeeBonus.md`  

---

<div align="center">
  <img src="https://media.giphy.com/media/du3J3cXyzhj75IOgvA/giphy.gif" width="420" alt="sql vibes gif"/>
</div>

---

## 🧾 Problem Statement (simple)
You have two tables:

**Employee**
| empId | name   | supervisor | salary |
|-------|--------|------------|--------|

**Bonus**
| empId | bonus  |
|-------|--------|

- `empId` links the two tables.
- Report the **name** and **bonus** of every employee who either:
  - has **bonus < 1000**, OR
  - has **no bonus record** (i.e., `bonus` is `NULL`).

**Return rows in any order.**

**Example input**
Employee:
```

(3, Brad, null, 4000)
(1, John, 3, 1000)
(2, Dan, 3, 2000)
(4, Thomas, 3, 4000)

```
Bonus:
```

(2, 500)
(4, 2000)

````

**Expected output**
| name  | bonus |
|-------|-------|
| Brad  | null  |
| John  | null  |
| Dan   | 500   |

---

## 💡 Key idea (one-liner)
- Use a **LEFT JOIN** from `Employee` → `Bonus` so all employees are included, then filter for `bonus < 1000 OR bonus IS NULL`.

---

# 🛠 Solutions 
---

### ✅ Solution 1
```sql
SELECT e.name, b.bonus
FROM Employee e
LEFT JOIN Bonus b ON e.empId = b.empId
WHERE b.bonus IS NULL OR b.bonus < 1000;
````

* **TLDR**

  * LEFT JOIN employees with bonuses, keep rows where bonus is missing or < 1000.

* **Approach**

  * Keep every employee record using a LEFT JOIN; the Bonus side will be `NULL` for employees without a bonus. Filter later.

* **Steps**

  1. SELECT `name` and `bonus`.
  2. LEFT JOIN `Bonus` on `empId`.
  3. Apply `WHERE b.bonus IS NULL OR b.bonus < 1000`.
  4. Return results.

* **Key Observations**

  * `LEFT JOIN` keeps employees with no bonus (they show `NULL`).
  * `IS NULL` catches employees not present in Bonus table.
  * Using `INNER JOIN` would drop those employees.

* **Complexity & Explanation**

  * **Time:** O(N) typical (depends on indexes and DB engine).
  * **Space:** O(result size).
  * **Explanation:** This is the standard and readable solution — join, then filter. It’s fast and correct for the problem requirement.

---

### ✅ Solution 2 (same logic, explicit naming)

```sql
SELECT Employee.name, Bonus.bonus
FROM Employee
LEFT JOIN Bonus ON Employee.empId = Bonus.empId
WHERE Bonus.bonus < 1000 OR Bonus.bonus IS NULL;
```

* **TLDR**

  * Same as Sol 1, written with full table names for clarity.

* **Approach**

  * Use LEFT JOIN, filter by `Bonus.bonus < 1000` or `IS NULL`.

* **Steps**

  1. LEFT JOIN Employee → Bonus.
  2. Filter bonus < 1000 or NULL.
  3. Select name and bonus.

* **Key Observations**

  * Using full table names can be easier to read for beginners.
  * Logic identical to Sol 1, no change in result.

* **Complexity & Explanation**

  * **Time:** O(N). **Space:** O(result).
  * **Explanation:** Readability-first variant; use this in tutorials or when clarity matters.

---

### ✅ Solution 3 (alias style)

```sql
SELECT e.name, b.bonus
FROM Employee e
LEFT JOIN Bonus b ON e.empId = b.empId
WHERE b.bonus < 1000 OR b.bonus IS NULL;
```

* **TLDR**

  * Same LEFT JOIN approach using table aliases (`e`, `b`).

* **Approach**

  * Aliases make the SQL shorter while keeping the join + filter idea.

* **Steps**

  1. Alias Employee as `e`, Bonus as `b`.
  2. LEFT JOIN on `empId`.
  3. Apply `WHERE b.bonus < 1000 OR b.bonus IS NULL`.

* **Key Observations**

  * Aliases are standard in production queries for compactness.
  * No semantic change: result ≡ Sol 1.

* **Complexity & Explanation**

  * **Time:** O(N). **Space:** O(result).
  * **Explanation:** Preferred concise style for real projects; functionally identical.

---

### ✅ Solution 4 (column order variant)

```sql
SELECT name, bonus
FROM Employee E
LEFT JOIN Bonus B ON B.empId = E.empId
WHERE bonus < 1000 OR bonus IS NULL;
```

* **TLDR**

  * Same logic, slightly different column/order styling.

* **Approach**

  * Keep the same LEFT JOIN + filter approach while selecting columns in a different order.

* **Steps**

  1. LEFT JOIN Employee → Bonus.
  2. Filter for `bonus < 1000 OR bonus IS NULL`.
  3. Select `name, bonus`.

* **Key Observations**

  * SQL formatting and column ordering won’t affect the correctness.
  * Useful if you want `name` first visually.

* **Complexity & Explanation**

  * **Time:** O(N). **Space:** O(result).
  * **Explanation:** Cosmetic variant; use whatever column order suits your UI.

---

### ✅ Solution 5 (detailed walkthrough style)

```sql
SELECT e.name, b.bonus
FROM Employee e
LEFT JOIN Bonus b ON e.empId = b.empId
WHERE b.bonus < 1000 OR b.bonus IS NULL;
```

* **TLDR**

  * Same as others, with an explicit walkthrough focus.

* **Approach**

  * Treat Employee as the base table and bring bonuses in when available; keep everyone then filter.

* **Steps**

  1. Start from Employee (all employees).
  2. LEFT JOIN Bonus to see who has a bonus.
  3. Keep rows where bonus is `NULL` or `< 1000`.
  4. Display `name` and `bonus`.

* **Key Observations**

  * This solution highlights the reasoning for why `LEFT JOIN` is required.
  * Good for explaining to non-technical stakeholders or juniors.

* **Complexity & Explanation**

  * **Time:** O(N). **Space:** O(result).
  * **Explanation:** The canonical teaching example — preserves all employees and only filters by bonus threshold.

---

### ✅ Solution 6 (final teaching variant)

```sql
SELECT e.name, b.bonus
FROM Employee e
LEFT JOIN Bonus b ON e.empId = b.empId
WHERE (b.bonus < 1000 OR b.bonus IS NULL);
```

* **TLDR**

  * Exactly same final logic; parentheses added for clarity.

* **Approach**

  * Use simple grouping of the WHERE condition to emphasize the OR relationship.

* **Steps**

  1. LEFT JOIN Employee → Bonus.
  2. Use parentheses to make the condition obvious: `(b.bonus < 1000 OR b.bonus IS NULL)`.
  3. Select `name, bonus`.

* **Key Observations**

  * Parentheses improve readability, especially when adding more conditions later.
  * Nothing changes logically.

* **Complexity & Explanation**

  * **Time:** O(N). **Space:** O(result).
  * **Explanation:** Best for readability and future extension (e.g., adding date or department filters).

---

# 📌 Final Notes (simple & human)

* All correct solutions use a **LEFT JOIN** (important!).
* The key filter is: `b.bonus IS NULL OR b.bonus < 1000`.
* `INNER JOIN` would **miss employees with no bonus** (wrong answer).
* These queries return the same result; pick the style that matches your codebase (aliases, explicit names, or parentheses for clarity).

---

<div align="center">
  <img src="https://media.giphy.com/media/26BRQTezZrKak4BeE/giphy.gif" width="380" alt="success gif"/>
  <p><small>If this helped, add it to your repo & flex in interviews 😎</small></p>
</div>
