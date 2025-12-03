Data Analyst Job Market Exploration

🔹 Introduction

This project explores the data analyst job market: top salaries, in-demand skills, and what actually boosts earning potential. The goal was to answer key career questions and make job hunting less chaotic.


🔹 Questions I Tackled

Which data analyst jobs pay the most?

What skills do those top-paying roles expect?

What skills are most requested across job postings?

Which skills increase salary potential?

What skills are the smartest to learn?


🔹 Tools Used

SQL for querying and analysis

SQL Server Management Studio to manage and explore databases

GitHub for version control and sharing scripts


🔹 Analysis & Queries

1. Top Paying Data Analyst Jobs
```
   SELECT TOP 10
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim 
    ON job_postings_fact.company_id = company_dim.company_id
WHERE job_title_short = 'Data Analyst'
    AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC;
```
✔ Results showed salaries ranging from $184K to $650K, with roles across major companies.


2. Skills Required for Top Paying Jobs
```
WITH top_paying_jobs AS (
    SELECT TOP 10
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim 
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE job_title_short = 'Data Analyst'
        AND job_location = 'Anywhere'
        AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
)
SELECT 
    top_paying_jobs.*,
    skills_dim.skills AS skills
FROM top_paying_jobs
INNER JOIN skills_job_dim 
    ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY top_paying_jobs.salary_year_avg DESC;
```

✔ SQL, Python, and Tableau appeared most frequently among high-paying roles.


3. Most In-Demand Skills
```
SELECT TOP 5
    skills_dim.skills AS skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim 
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
    AND job_work_from_home = 1
GROUP BY skills_dim.skills
ORDER BY demand_count DESC;
```

✔ SQL and Excel dominated demand, followed by Python, Tableau, and Power BI.


4. Salary by Skill
  ``` 
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim 
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = 1
GROUP BY skills
ORDER BY avg_salary DESC;
```

✔ Skills related to big data, Python libraries, and cloud tools showed the highest salary averages.


5. Optimal Skills (High Salary + High Demand)
```
WITH skills_demand AS (
    SELECT
        s.skill_id,
        s.skills AS skills,
        COUNT(sj.job_id) AS demand_count
    FROM job_postings_fact AS j
    INNER JOIN skills_job_dim AS sj 
        ON j.job_id = sj.job_id
    INNER JOIN skills_dim AS s 
        ON sj.skill_id = s.skill_id
    WHERE j.job_title_short = 'Data Analyst'
        AND j.salary_year_avg IS NOT NULL
        AND j.job_work_from_home = 1
    GROUP BY s.skill_id, s.skills
),
average_salary AS (
    SELECT 
        sj.skill_id,
        ROUND(AVG(TRY_CAST(j.salary_year_avg AS FLOAT)), 0) AS avg_salary
    FROM job_postings_fact AS j
    INNER JOIN skills_job_dim AS sj 
        ON j.job_id = sj.job_id
    WHERE j.job_title_short = 'Data Analyst'
        AND j.salary_year_avg IS NOT NULL
        AND j.job_work_from_home = 1
    GROUP BY sj.skill_id
)
SELECT TOP 25
    sd.skill_id,
    sd.skills,
    sd.demand_count,
    a.avg_salary
FROM skills_demand AS sd
INNER JOIN average_salary AS a 
    ON sd.skill_id = a.skill_id
WHERE sd.demand_count > 10
ORDER BY a.avg_salary DESC, sd.demand_count DESC;
```

✔ Cloud tools, BI platforms, Python, database skills, and Java stood out as high-value skills.



🔹 What I Learned

Advanced SQL querying using joins, CTEs, aggregation, and ranking

Interpreting real-world job market data

Connecting skill trends to salary and demand insights




🔹 Conclusion

SQL is the most demanded and valuable skill.

Specialized skills like cloud platforms and big data tools increase salary potential.

Understanding job trends helps prioritize skill development for competitive advantage.

This project strengthened my SQL knowledge and gave me a clearer understanding of how skills influence career growth in data analytics.

   
