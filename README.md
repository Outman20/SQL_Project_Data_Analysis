# Introduction

This project is a SQL-based data analysis project focused on exploring the data science job market. 📊

The analysis uses job posting data to identify trends in salaries, in-demand skills, and remote work opportunities, providing insights into the current demand for data-related roles. 💼

The project demonstrates how SQL can be used to explore and analyze real-world job market data and turn it into meaningful insights. 🧠

SQL queries? Check them out here: [project_sql folder](/project_sql/)

# Background 🎯

I created this project to better understand the **data science job market** and explore which skills and qualifications are most valuable for data-related careers. 💼

The analysis focuses on job postings and explores questions such as:

* 💰 Which data roles offer the highest salaries?
* 🛠️ Which skills are most in demand?
* 📈 Which skills are associated with higher-paying jobs?
* 🌍 How common are remote opportunities?
* 🎓 What can the job market tell us about the skills needed to build a career in data?

By answering these questions with SQL, I wanted to gain practical experience working with a large dataset while developing a better understanding of the current data job market. 🔎

# Tools I Used 🛠️

* 🐘 **PostgreSQL** — Used to query, filter, join, and analyze the job posting data.
* 💻 **SQL** — Used as the main tool for data exploration and analysis, including CTEs, aggregations, joins, and subqueries.
* 🗄️ **pgAdmin** — Used to manage the PostgreSQL database and execute SQL queries.
* 🐙 **Git & GitHub** — Used for version control and to document and showcase the project.

# The Analysis

The analysis explores different aspects of the data science job market using SQL. The main focus areas include:

### 1. 💰 Top-Paying Jobs

Identifying the highest-paying data roles and exploring salary differences across positions.
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
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Scientist' AND 
    job_location = 'Anywhere' AND 
    salary_year_avg IS NOT NULL
    ORDER BY  salary_year_avg DESC
    LIMIT 10
```

- 💰 The highest-paying Data Scientist roles can reach salaries of over **$500K per year**.
- 👔 The highest salaries are often associated with **senior and leadership positions**, such as Director or Head of Data Science.
- 🌎 Remote Data Scientist positions can offer highly competitive salaries across different industries.
### 2. 🛠️ In-Demand Skills

Finding the skills that appear most frequently in data science job postings.
```sql
WITH top_paying_jobs AS (
    SELECT 
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Scientist' AND 
        job_location = 'Anywhere' AND 
        salary_year_avg IS NOT NULL
    ORDER BY  salary_year_avg DESC
    LIMIT 10
)


SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id 
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY 
    salary_year_avg DESC


SELECT 
    job_id,
    job_title,
    salary_year_avg,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Scientist' AND 
    job_location = 'Anywhere' AND 
    salary_year_avg IS NOT NULL
    ORDER BY  salary_year_avg DESC
    LIMIT 10
```

**💡 Key Insights:**

- 🐍 **Python** is by far the most in-demand skill for Data Scientists.
- 🗄️ **SQL** ranks second, highlighting the importance of strong data querying skills.
- 📊 **R, AWS, and Tableau** also appear frequently, showing the importance of statistical analysis, cloud computing, and data visualization.
### 3. 📈 Top-Paying Skills

Analyzing which skills are associated with the highest average salaries.

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id 
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Scientist' AND
    job_work_from_home = True
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5
```

**💡 Key Insights:**

- 💰 Some less common or specialized skills are associated with very high average salaries.
- ☁️ Technologies related to **cloud infrastructure and advanced data systems** appear among the higher-paying skills.
- 🧠 The results suggest that specialized technical knowledge can be associated with higher compensation, although salary should not be interpreted as being caused by a particular skill alone.

### 4. 🎯 Optimal Skills

Combining skill demand and salary data to identify skills that offer both strong market demand and high earning potential.

```sql
SELECT 
    skills,
    ROUND (AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id 
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Scientist' 
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25
```

- ☁️ **Snowflake, GCP, and BigQuery** combine relatively strong demand with average salaries above **$150K** in the analyzed dataset.
- ⚙️ Skills such as **PyTorch, Airflow, and Scala** also show a strong combination of demand and salary.
- 🐍 **Python and SQL** remain important foundational skills because of their high demand, even though they are not necessarily the highest-paying skills individually.

### 5. 🌍 Remote Opportunities
Exploring job postings that offer work-from-home opportunities.

The analysis uses SQL techniques such as JOINs, CTEs, aggregations, filtering, sorting, and subqueries to transform raw job posting data into actionable 
insights.

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
- 🌎 Remote opportunities represent a significant part of the analyzed Data Scientist job market.
- 💻 Remote positions cover a wide range of salaries and required skills.
- 🛠️ The skills associated with remote roles largely overlap with the broader Data Scientist market, particularly Python, SQL, cloud technologies, and machine learning tools.
# What I Learned 

* 🗄️ Improved my SQL skills by working with **JOINs, CTEs, subqueries, aggregations, filtering, and sorting**.
* 🔍 Gained practical experience exploring a **large real-world dataset** and identifying patterns and trends.
* 📊 Learned how to turn SQL query results into **meaningful and actionable insights**.
* 💼 Developed a better understanding of the **data science job market**, including salaries, in-demand skills, and remote opportunities.
* 🚀 Improved my ability to approach data analysis by starting with a question and using data to find an answer.


# Conclusions

### 📊 Insights

* 💰 Salary varies significantly across different data-related roles and skills.
* 🛠️ Some technical skills appear frequently across job postings, highlighting their importance in the data job market.
* 📈 High-paying skills are not always the most in-demand, showing that **salary and demand are two different factors** to consider.
* 🌍 Remote opportunities represent an important part of the data job market, although availability varies by role.

### 💭 Closing Thoughts

This project helped me understand how **SQL can be used to turn raw job posting data into meaningful insights**. It also gave me a better perspective on the skills and opportunities currently present in the data science job market.

Most importantly, this project was an opportunity to apply SQL to a **real-world dataset** and strengthen my analytical thinking while building a practical data portfolio project. 🚀
