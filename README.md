# Data-Cleaning-Portfolio-Project-Queries.sql
CREATE TABLE layoffs_staging
LIKE layoffs; 

SELECT *
FROM layoffs_staging;

INSERT layoffs_staging
SELECT *
FROM layoffs;

SELECT *,
 row_number() OVER(
 partition by company, location, industry, total_laid_off, `date`, stage, country, funds_raised) AS row_num
FROM layoffs_staging;

WITH duplicate_cte AS 
(
SELECT *,
 row_number() OVER(
 partition by company, location, industry, total_laid_off, `date`, stage, country, funds_raised) AS row_num
FROM layoffs_staging
)
SELECT * 
FROM duplicate_cte
WHERE row_num > 1;

SELECT * 
FROM layoffs_staging 
WHERE company = 'cazoo';


WITH duplicate_cte AS 
(
SELECT *,
 row_number() OVER(
 partition by company, location, industry, total_laid_off, `date`, stage, country, funds_raised) AS row_num
FROM layoffs_staging
)
DELETE 
FROM duplicate_cte
WHERE row_num > 1;





CREATE TABLE `layoffs_staging3` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` bigint DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised` bigint DEFAULT NULL,
  `row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;


SELECT * 
FROM layoffs_staging3
WHERE row_num > 1 
;

INSERT INTO layoffs_staging3
SELECT *,
 row_number() OVER(
 partition by company, location, industry, total_laid_off, `date`, stage, country, funds_raised) AS row_num
FROM layoffs_staging;


DELETE 
FROM layoffs_staging3
WHERE row_num > 1 
;

SELECT * 
FROM layoffs_staging 
WHERE company = 'cazoo';

SELECT * 
FROM layoffs_staging3
;
--------------------------------------------------

-- Standandrizing data 

SELECT company, trim(company) 
FROM layoffs_staging3
;
update layoffs_staging3
SET company = trim(company) ;

SELECT distinct(industry) 
FROM layoffs_staging3
ORDER BY 1
;

SELECT distinct(country), trim(TRAILING '.' FROM country)   
FROM layoffs_staging3
ORDER BY 1 ;

UPDATE layoffs_staging3
SET country = trim(TRAILING '.' FROM country)
WHERE country LIKE 'united states%' ;


SELECT `date`, date_format(`date`, '%Y/%m/%d')
FROM layoffs_staging3
;

UPDATE layoffs_staging3
SET `date` = date_format(`date`, '%Y/%m/%d');

SELECT `date`
FROM layoffs_staging3
;

ALTER TABLE layoffs_staging3
MODIFY COLUMN `date` DATE ;

SELECT *
FROM layoffs_staging3 ;
