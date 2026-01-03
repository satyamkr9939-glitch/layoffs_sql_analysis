# layoffs_sql_analysis
Objective: to clean and analyze world's layoffs data
Tool used : My SQL and Excel etc
Steps Taken : ( Removed Duplicates , Stanardize Data Format, Handed null values.

SELECT * FROM layoffs;

-- 1 remove duplicates
-- 2 sandardize the data
-- 3 null valuse or blank values
-- 4 remove any columns


create table layoffsa_staging
like layoffs;

insert layoffsa_staging
select * 
from layoffs;


select *,
 row_number() OVER(
PARTITION BY company, industry, total_laid_off, percentage_laid_off, 'data') as row_num
 from layoffsa_staging;
 
 with duplicate_ctc as
 (
 select *,
 row_number() OVER(
PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, 'data', stage , country , funds_raised_millions) as row_num
 from layoffsa_staging
 ) select * from duplicate_ctc
 where row_num>1;



with duplicate_ctc as
 (
 select *,
 row_number() OVER(
PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, 'data', stage , country , funds_raised_millions) as row_num
 from layoffsa_staging
 ) 
 delete
 from duplicate_ctc
 where row_num>1;

CREATE TABLE `layoffsa_staging2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL, 
   `row_num` int
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

select * 
from layoffsa_staging2;

insert into layoffsa_staging2
select*,
row_number() OVER(
PARTITION BY company, location, industry, total_laid_off, 
percentage_laid_off, 'data', stage , country , funds_raised_millions) as row_num
 from layoffsa_staging;



delete
from layoffsa_staging2
where row_num >1;


select * 
from layoffsa_staging2
where row_num >1;

select * from layoffsa_staging2;


-- sandardizing data

select company, trim(company)
from layoffsa_staging2;

update layoffsa_staging2
set company = trim(company);

select distinct industry
from layoffsa_staging2;


update layoffsa_staging2
set industry = 'crypto'
where industry like 'crypto%'; 

select distinct country, trim(trailing '.' from country)
from layoffsa_staging2
 order by 1;

update layoffsa_staging2
set country = trim(trailing '.' from country)
where country like 'united states%';
select *
from layoffsa_staging2;
select `date`,
str_to_date(`date`, '%m/%d/%Y')
from layoffsa_staging2;




select `str_to_date`,
str_to_date(`str_to_date`, '%m/%d/%Y')
from layoffsa_staging2;




select `date`,
str_to_date(`date` , '%m-%d-%Y')
from layoffsa_staging2;

UPDATE layoffsa_staging2
SET `DATE` = str_to_date(`DATE`, '%m/%d/%Y');

		select `date`  
		from layoffsa_staging2;
        
select date_format(`date`, '%y-%m-%d')
as formatted_date
from layoffsa_staging2;

 
select
str_to_date(trim(`date`), 'm%/d%/Y')
from layoffsa_staging2;

select
case
when `date` like '_/_/_' then 
str_to_date(`date` , '%m/%d/%y')
when `date` like '-_-_-' then
str_to_date(`date` , '%m-%d-%y')
else null
end as cleaned_date
from layoffsa_staging2;

select coalesce(
str_to_date(`date`, '%m/%d/%Y'),
str_to_date(`date`, '%Y-%m-%d'),
str_to_date(`date`, '%d-%m-%Y')
) as cleaned_date
from layoffsa_staging2;

select *
from layoffsa_staging2;

update layoffsa_staging2
set `date` = cleaned_date(`date`, '%y-%m-%d');


update layoffsa_staging2
set `date` = str_to_date(`date`, '%Y/%m/5d');

select * 
from layoffsa_staging2
 where total_laid_off is null
 and percentage_laid_off is null;
 
select *
from layoffsa_staging2;
 
  update layoffsa_staging2 t1
 join layoffsa_staging2 t2
    on t1.company = t2.company
    set t1.industry = t2.industry
 where (t1.industry is null or t1.industry ='') 
 and t2.industry is not null;
 
 update layoffsa_staging2
 set industry = null
 where industry = '';

  select t1.industry, t2.industry
  from layoffsa_staging2 t1
 join layoffsa_staging2 t2
    on t1.company = t2.company
 where t1.industry is null 
 and t2.industry is not null;

select *
from layoffsa_staging2;

alter table layoffsa_staging2
drop column row_num;


delete 
from layoffsa_staging2 
where total_laid_off is null
and percentage_laid_off is null;
