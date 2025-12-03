Data Analyst Job Market Insights Project
Introduction

This project explores the data analyst job market to understand salaries, required skills, and the most valuable skills for aspiring analysts.

Background

To navigate the job market effectively, this analysis focuses on these questions:

Which data analyst roles pay the highest salaries?

What skills are required for these roles?

Which skills are most in demand?

Which skills correlate with higher salaries?

What skills should aspiring analysts prioritize learning?

Tools Used

SQL

SQL Server Management Studio

GitHub

Analysis
1. Top-Paying Data Analyst Jobs
SELECT TOP 10
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN 
    company_dim 
    ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' 
    AND job_location = 'Anywhere' 
    AND salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC;


Insights:

Salaries range from 184,000 to 650,000 dollars

Employers include Meta, AT&T, and SmartAsset

Wide variation of job titles within analytics

2. Skills Required for Top-Paying Roles
WITH top_paying_jobs AS (
    SELECT TOP 10
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN 
        company_dim 
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' 
        AND job_location = 'Anywhere' 
        AND salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
)

SELECT 
    top_paying_jobs.*,
    skills_dim.skills AS skills
FROM 
    top_paying_jobs
INNER JOIN 
    skills_job_dim 
        ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN 
    skills_dim 
        ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    top_paying_jobs.salary_year_avg DESC;


Key skills among top earners:

SQL

Python

Tableau

R, Snowflake, Pandas, Excel

3. Most In-Demand Skills for Data Analysts
SELECT TOP 5
    skills_dim.skills AS skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM 
    job_postings_fact
INNER JOIN 
    skills_job_dim 
        ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN 
    skills_dim 
        ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND job_work_from_home = 1
GROUP BY
    skills_dim.skills
ORDER BY
    demand_count DESC;


Top 5 in-demand skills:

Skill	Demand Count
SQL	7291
Excel	4611
Python	4330
Tableau	3745
Power BI	2609
4. Skills Associated With Higher Salaries
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim 
        ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
        ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = 'True'
GROUP BY
    skills
ORDER BY
    avg_salary DESC
OFFSET 0 ROWS FETCH NEXT 25 ROWS ONLY;


Most lucrative skills include:

PySpark

Couchbase

GitLab

Jupyter

Pandas

Elasticsearch

5. Optimal Skills to Learn
WITH skills_demand AS (
    SELECT
        s.skill_id,
        s.skills AS skills,
        COUNT(sj.job_id) AS demand_count
    FROM 
        job_postings_fact AS j
    INNER JOIN 
        skills_job_dim AS sj 
            ON j.job_id = sj.job_id
    INNER JOIN 
        skills_dim AS s 
            ON sj.skill_id = s.skill_id
    WHERE
        j.job_title_short = 'Data Analyst'
        AND j.salary_year_avg IS NOT NULL
        AND j.job_work_from_home = 'True' 
    GROUP BY
        s.skill_id,
        s.skills
), 
average_salary AS (
    SELECT 
        sj.skill_id,
        ROUND(AVG(TRY_CAST(j.salary_year_avg AS FLOAT)), 0) AS avg_salary
    FROM 
        job_postings_fact AS j
    INNER JOIN 
        skills_job_dim AS sj 
            ON j.job_id = sj.job_id
    INNER JOIN 
        skills_dim AS s 
            ON sj.skill_id = s.skill_id
    WHERE
        j.job_title_short = 'Data Analyst'
        AND j.salary_year_avg IS NOT NULL
        AND j.job_work_from_home = 'True'    
    GROUP BY
        sj.skill_id
)
SELECT TOP 25
    sd.skill_id,
    sd.skills,
    sd.demand_count,
    a.avg_salary
FROM
    skills_demand AS sd
INNER JOIN  
    average_salary AS a 
        ON sd.skill_id = a.skill_id
WHERE  
    sd.demand_count > 10
ORDER BY
    a.avg_salary DESC,
    sd.demand_count DESC;


High-value skill areas:

Cloud tech (AWS, Snowflake, Azure, BigQuery)

Programming (Python, Java, Go, R)

BI platforms (Tableau, Looker)

Data engineering tools (SSIS, Hadoop)

What I Learned

Writing complex queries using joins and CTEs

Using aggregate functions to extract meaning from data

Turning analytical questions into SQL-driven insights

Conclusions

Top remote analyst salaries can reach 650,000 dollars

SQL is the most required and most demanded skill

Cloud and big data skills deliver higher average salaries

Optimizing skill development requires blending demand and salary insights

Final Thoughts

This analysis strengthened my SQL foundation and gave clarity on market needs. Understanding what skills matter most helps analysts focus their learning and improve their job prospects.
