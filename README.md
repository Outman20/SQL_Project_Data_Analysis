# Introduction

This project is a SQL-based data analysis project focused on exploring the data science job market. 📊

The analysis uses job posting data to identify trends in salaries, in-demand skills, and remote work opportunities, providing insights into the current demand for data-related roles. 💼

The project demonstrates how SQL can be used to explore and analyze real-world job market data and turn it into meaningful insights. 🧠

SQL queries? Check them out here: [project_sql folder](/project_sql/)

# Background
This project is a SQL-based data analysis project focused on exploring the data science job market. 📊

The analysis uses job posting data to identify trends in salaries, in-demand skills, and remote work opportunities, providing insights into the current demand for data-related roles. 💼

The project demonstrates how SQL can be used to explore and analyze real-world job market data and turn it into meaningful insights. 🧠
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
### 2. 🛠️ In-Demand Skills

Finding the skills that appear most frequently in data science job postings.

### 3. 📈 Top-Paying Skills

Analyzing which skills are associated with the highest average salaries.

### 4. 🎯 Optimal Skills

Combining skill demand and salary data to identify skills that offer both strong market demand and high earning potential.

### 5. 🌍 Remote Opportunities

Exploring job postings that offer work-from-home opportunities.

The analysis uses SQL techniques such as JOINs, CTEs, aggregations, filtering, sorting, and subqueries to transform raw job posting data into actionable insights.

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
