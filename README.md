# Introduction

This project is a SQL-based data analysis project focused on exploring the **Data Science job market**. 📊

The analysis uses job posting data to identify trends in **salaries, in-demand skills, and the relationship between skills, demand, and salary**. 💼

The project demonstrates how SQL can be used to explore and analyze real-world job market data and turn it into meaningful insights. 🧠

SQL queries? Check them out here: [project_sql folder](/project_sql/)

# Background 🎯

I created this project to better understand the **Data Science job market** and explore which skills and qualifications are most valuable for data-related careers. 💼

The analysis focuses on Data Scientist job postings and explores questions such as:

* 💰 Which Data Scientist roles offer the highest salaries?
* 🛠️ Which skills appear in the highest-paying Data Scientist jobs?
* 📊 Which skills are most in demand?
* 💵 Which skills are associated with higher average salaries?
* 🎯 Which skills combine strong demand with high earning potential?

By answering these questions with SQL, I wanted to gain practical experience working with a large dataset while developing a better understanding of the current Data Science job market. 🔎

# Tools I Used 🛠️

* 🐘 **PostgreSQL** — Used to query, filter, join, and analyze the job posting data.
* 💻 **SQL** — Used as the main tool for data exploration and analysis, including CTEs, aggregations, joins, filtering, sorting, and subqueries.
* 🗄️ **pgAdmin** — Used to manage the PostgreSQL database and execute SQL queries.
* 🐙 **Git & GitHub** — Used for version control and to document and showcase the project.

# The Analysis

The analysis explores different aspects of the Data Science job market using SQL.

### 1. 💰 Top-Paying Jobs

Identifying the highest-paying Data Scientist roles and exploring salary differences across positions.

```sql
SELECT 
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim 
    ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Scientist' 
    AND job_location = 'Anywhere' 
    AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```

**💡 Key Insights:**

- 💰 The highest-paying Data Scientist roles in the dataset offer very high annual salaries.
- 👔 Senior and leadership positions appear among the highest-paying roles.
- 🌎 Remote Data Scientist positions can offer highly competitive salaries.

---

### 2. 🛠️ Skills in Top-Paying Jobs

Examining which skills are associated with the **10 highest-paying Data Scientist jobs**.

```sql
WITH top_paying_jobs AS (
    SELECT 
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim 
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Scientist' 
        AND job_location = 'Anywhere' 
        AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim 
    ON top_paying_jobs.job_id = skills_job_dim.job_id 
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```

**💡 Key Insights:**

- 🐍 **Python** appears across several of the highest-paying Data Scientist positions.
- 🗄️ **SQL** is also present in multiple high-paying roles, highlighting its relevance even in highly compensated positions.
- 🤖 The highest-paying jobs require a combination of programming, data, cloud, and machine learning skills rather than relying on a single technology.

---

### 3. 📊 In-Demand Skills

Identifying the skills that appear most frequently in **remote Data Scientist job postings**.

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim 
    ON job_postings_fact.job_id = skills_job_dim.job_id 
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Scientist' 
    AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```

**💡 Key Insights:**

- 🐍 **Python** is one of the most frequently requested skills in remote Data Scientist positions.
- 🗄️ **SQL** is also highly demanded, showing the importance of data querying and database skills.
- 🧠 The most frequently requested skills cover programming, data analysis, and machine learning technologies.

---

### 4. 💵 Top-Paying Skills

Analyzing which skills are associated with the **highest average salaries** among Data Scientist job postings.

```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim 
    ON job_postings_fact.job_id = skills_job_dim.job_id 
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Scientist' 
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25;
```

**💡 Key Insights:**

- 💰 Some specialized skills are associated with significantly higher average salaries.
- ☁️ Cloud and advanced data technologies appear among the higher-paying skills.
- 📈 Salary levels can vary considerably depending on the skills associated with a Data Scientist position.

---

### 5. 🎯 Optimal Skills

Combining **skill demand and average salary** to identify skills that offer a strong balance between market demand and earning potential.

```sql
WITH skills_demand AS (

    SELECT 
        skills_job_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count

    FROM job_postings_fact

    INNER JOIN skills_job_dim 
        ON job_postings_fact.job_id = skills_job_dim.job_id

    INNER JOIN skills_dim 
        ON skills_job_dim.skill_id = skills_dim.skill_id

    WHERE 
        job_title_short = 'Data Scientist'
        AND job_work_from_home = TRUE
        AND salary_year_avg IS NOT NULL

    GROUP BY 
        skills_job_dim.skill_id,
        skills_dim.skills
),

average_salary AS (

    SELECT 
        skills_job_dim.skill_id,
        skills_dim.skills,
        ROUND(AVG(salary_year_avg), 0) AS avg_salary

    FROM job_postings_fact

    INNER JOIN skills_job_dim 
        ON job_postings_fact.job_id = skills_job_dim.job_id 

    INNER JOIN skills_dim 
        ON skills_job_dim.skill_id = skills_dim.skill_id

    WHERE 
        job_title_short = 'Data Scientist'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE

    GROUP BY
        skills_job_dim.skill_id,
        skills_dim.skills
)

SELECT 
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary

FROM skills_demand

INNER JOIN average_salary 
    ON skills_demand.skill_id = average_salary.skill_id

WHERE demand_count > 10

ORDER BY
    avg_salary DESC,
    demand_count DESC

LIMIT 25;
```

**💡 Key Insights:**

- ☁️ **Snowflake, GCP, and BigQuery** combine relatively strong demand with high average salaries in the analyzed dataset.
- ⚙️ Skills such as **PyTorch, Airflow, and Scala** also show a strong combination of demand and salary.
- 🐍 **Python and SQL** remain important foundational skills because of their high demand, even though they are not necessarily the highest-paying skills individually.

# What I Learned

* 🗄️ Improved my SQL skills by working with **JOINs, CTEs, subqueries, aggregations, filtering, and sorting**.
* 🔍 Gained practical experience exploring a **large real-world dataset** and identifying patterns and trends.
* 📊 Learned how to turn SQL query results into **meaningful insights**.
* 💼 Developed a better understanding of the **Data Science job market**, including salaries and in-demand skills.
* 🎯 Learned that **skill demand and salary are different factors** that can be analyzed together to identify valuable skills.
* 🚀 Improved my ability to approach data analysis by starting with a question and using data to find an answer.

# Conclusions

### 📊 Insights

* 💰 Data Scientist salaries can vary significantly across different roles and skills.
* 🛠️ **Python and SQL** are among the most frequently requested skills in the analyzed job postings.
* 📈 The most highly paid skills are not necessarily the most in-demand, showing that **salary and demand are two different factors** to consider.
* 🎯 Combining demand and salary provides a more complete view of the potential value of different technical skills.
* ☁️ Cloud, machine learning, and data engineering technologies appear throughout the analysis, reflecting the broad technical skill set required in Data Science.

### 💭 Closing Thoughts

This project helped me understand how **SQL can be used to turn raw job posting data into meaningful insights**. It also gave me a better perspective on the skills and salary patterns present in the Data Science job market.

Most importantly, this project was an opportunity to apply SQL to a **real-world dataset** and strengthen my analytical thinking while building a practical data portfolio project. 