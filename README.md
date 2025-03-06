# Student Streaks Analysis with SQL

## 📌 Project Overview
Tracking user engagement is a crucial aspect of EdTech businesses, and **streaks** (consecutive active days) play a significant role in understanding user behavior. This project focuses on analyzing student streaks using SQL, helping identify the most engaged learners on the **365 Data Science** platform.

By analyzing streak patterns, businesses can extract meaningful insights, such as:
- Recognizing top users for testimonials and feedback.
- Understanding engagement trends to improve user experience.
- Enhancing product features based on real user data.

## 🏗️ Database Structure
We work with a **MySQL database** that includes a `user_streaks_sql` table, which contains the following fields:

| Column Name       | Description |
|------------------|-------------|
| `streak_id`      | Unique identifier for each streak record |
| `user_id`        | Identifier for each user |
| `streak_active`  | Boolean: **True** if the streak is active, **False** otherwise |
| `streak_frozen`  | Boolean: **True** if the streak is frozen, **False** otherwise |
| `streak_platform`| Platform where the streak was recorded |
| `streak_created` | Timestamp of when the streak started/updated |

The **goal** is to calculate each user's **longest streak** and extract users who maintained a streak of **30+ days**.

---

## ⚙️ Requirements
To run this project, ensure you have:
- **MySQL 8.0+** installed
- **MySQL Workbench** for executing queries

---

## 🔍 Steps to Analyze User Streaks
### **Part 1: Calculating Longest Streak**
#### 🔹 Step 1: Explore the Data Structure
First, examine the table structure:
```sql
DESCRIBE user_streaks_sql;
```
Then, retrieve a few records:
```sql
SELECT * FROM user_streaks_sql LIMIT 10;
```
This helps understand how streaks are stored.

#### 🔹 Step 2: Initialize Variables
Before calculating streaks, initialize user-defined variables:
```sql
SET @streak_count := 0;
SET @prev_user_id := NULL;
SET @prev_streak_active := NULL;
```
These variables help track streak progression across rows.

#### 🔹 Step 3: Compute Streak Lengths
Now, create a **temporary table** to calculate streak lengths:
```sql
CREATE TEMPORARY TABLE streaks_new AS (
  SELECT
    user_id,
    streak_created,
    streak_active,
    streak_frozen,
    (
      CASE
        WHEN @prev_user_id = user_id AND @prev_streak_active = 1 AND streak_active = 1
        THEN @streak_count := @streak_count + 1
        ELSE @streak_count := 0
      END
    ) AS streak_length,
    @prev_user_id := user_id,
    @prev_streak_active := streak_active
  FROM
    user_streaks_sql  
  ORDER BY
    user_id, streak_created
);
```
This logic increments the streak count **if the user remains active**; otherwise, it resets to **zero**.

---

### **Part 2: Ranking Top Users**
#### 🔹 Step 4: Extract Users with 30+ Day Streaks
To identify the most engaged students:
```sql
SELECT
  user_id,
  MAX(streak_length) AS max_streak_length
FROM
  streaks_new
GROUP BY
  user_id
HAVING
  MAX(streak_length) >= 30
ORDER BY
  max_streak_length DESC;
```
This query selects users who have achieved streaks **30+ days** and sorts them by streak length.

---

## 📊 Key Insights
- Users with streaks **30+ days** are highly engaged and can provide valuable feedback.
- Understanding streak patterns helps improve platform features and user retention.
- Businesses can leverage this data to personalize marketing and reward consistent learners.

---
## 💡 Conclusion
This project showcases how **SQL** can be leveraged to track user engagement through streak analysis. By identifying high-value users, EdTech businesses can optimize their strategies to enhance learner experiences.

**Thanks! 🚀**

