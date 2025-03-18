# Layoffs-Analysiss
This project analyze layoffs trend across different companies and industries , the impact of layoffs on company size, year and industry with the help of Power BI and Mysql insights are also drawn.

## 📂 Files Included  
- `layoffs.csv` - The dataset used for analysis.  
- `layoffs.sql` - SQL queries used for data cleaning and transformation.  
- `layoffs visualization.pbix` - Power BI file with dashboards and insights.  

## 📊 Key Visualizations  
- **Layoffs Trend Over Time** 📅  
- **Layoffs vs Company Size** 🔍  
- **Industry-wise Layoff Impact** 💼  

## 🚀 How to Use  
1. Open `layoffs visualization.pbix` in Power BI.  
2. Explore dashboards and insights.  

## 🛠️ Tools Used  
- **SQL** - Data cleaning and transformation.  
- **Power BI** - Visualization and reporting.  
- **GitHub** - Version control and collaboration.  

---
💡 *Feel free to contribute or provide suggestions!* 

SELECT * FROM layoffs;


select *,
row_number() over(
partition by company, location, industry, total_laid_off, percentage_laid_off, 'date'
) as row_num
from layoffs_staging;


with duplicate_cte as 
(
select *,
row_number() over(
partition by company, location, industry, total_laid_off, percentage_laid_off, 'date'
) as row_num
from layoffs_staging
)
select * 
from duplicate_cte
where row_num > 1;

select * 
from layoffs_staging
where company = 'oda' ;

select * 
from layoffs_staging
where company = 'Casper';

CREATE TABLE `layoffs_staging2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;


select * from 
layoffs_staging2;

insert into layoffs_staging2
select *, 
row_number() over(partition by
company, location, industry, total_laid_off, percentage_laid_off, 'date', 
stage, country, funds_raised_millions) as row_num
from layoffs_staging;  

select * 
from layoffs_staging2
where row_num > 1;

delete  
from layoffs_staging2
where row_num > 1;

select * 
from layoffs_staging2
where row_num > 1;

-- standardizing data

select distinct company ,trim(company) 
from layoffs_staging2;

update layoffs_staging2
set company = trim(company);

select distinct industry
from layoffs_staging2
order by 1;

select * 
from layoffs_staging2
where industry like 'Crypto%';

update layoffs_staging2
set industry = 'Crypto'
where industry like 'Crypto%';

select 
distinct industry
from layoffs_staging2
order by 1;

select 
distinct location
from layoffs_staging
order by 1;

select * 
from layoffs_staging2
where left(country, 1) in ('U') 
order by 1; 

select distinct country, trim(trailing '.' from country) as new_c
from layoffs_staging2
order by 1;

update layoffs_staging2
set country = trim(trailing '.' from country)
where left(country, 1) in ('U');

select * 
from layoffs_staging2;

select `date`,
str_to_date(`date`, '%m/%d/%Y')
from layoffs_staging2;

update layoffs_staging2
set `date` = str_to_date(`date`, '%m/%d/%Y');

alter table layoffs_staging2
modify column `date` DATE;

select * 
from layoffs_staging2 
where total_laid_off is null 
and percentage_laid_off is null;

update layoffs_staging2 
set industry = null
where industry = '';

select *
from layoffs_staging2 
where industry is null
or industry = '';

select * 
from layoffs_staging2 
where company = 'Airbnb';

-- self join

select * 
from layoffs_staging2 t1
join layoffs_staging2 t2
on t1.company = t2.company
and t1.location = t2.location   
where (t1.industry is null or t1.industry = '')
and t2.industry is not null;

update layoffs_staging2 t1
join layoffs_staging2 t2
on t1.company = t2.company
set t1.industry = t2.industry
where t1.industry is null 
and t2.industry is not null;

delete 
from layoffs_staging2 
where total_laid_off is null
and percentage_laid_off is null;

select * 
from layoffs_staging2; 

alter table layoffs_staging2
drop column row_num;
