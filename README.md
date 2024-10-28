# Data Cleaning with SQL: Streamlining and Standardizing Real-World Data
This project showcases a professional approach to data cleaning using SQL on a dataset involving key business metrics. The project demonstrates techniques for refining raw data, removing inconsistencies, and creating a uniform, reliable dataset for data analysis and reporting. Through this process, we ensure that data is accurate, clean, and ready for high-level decision-making.

**Table of Contents**

1) Project Overview
2) Dataset Description
3) Data Cleaning Steps
* 1. Removing Duplicates
* 2. Standardizing Data and Fixing Errors
* 3. Removing Unnecessary Columns and Data
4) Conclusion

**Project Overview**

This project addresses common data quality issues, such as duplicate entries, missing values, and inconsistent formatting across key columns. Using SQL, we applied a series of cleaning and standardization techniques to ensure each data point is valid and conforms to a consistent format. These steps facilitate easier analysis and provide a polished dataset for further processing, reporting, or integration into business intelligence workflows.

**Dataset Description**

The dataset used in this project contains the following core attributes:

**company:** Name of the company

**location:** Location of the company 

**industry:** Industry category

**total_laid_off:** Number of employees impacted

**percentage_laid_off:** Percentage of workforce affected

**date:** Date associated with the event

**stage:** Business stage (e.g., startup, established)

**country:** Country of company operation

**funds_raised_millions:** Total funds raised by the company (in millions)

**Data Cleaning Steps**

**1. Removing Duplicates**

To ensure that each record is unique:

* Created a staging table to work on a copy of the original data, ensuring that the raw data remained untouched.
* Used SQL's **ROW_NUMBER()** function with **PARTITION BY** to identify duplicate entries based on key fields like company name, location, industry, and date.
* Deleted redundant records by retaining only the first occurrence of each duplicate.

       -- Identify and mark duplicates
       WITH duplicate_cte AS (
           SELECT *,
               ROW_NUMBER() OVER (
                   PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions
               ) AS row_num
           FROM data_staging
       )
       -- Delete duplicates in the final table
       DELETE FROM data_staging2
       WHERE row_num > 1;

**2. Standardizing Data and Fixing Errors**

To achieve a consistent dataset:

* **Null Handling:** Replaced blank fields with **NULL** values to simplify handling in queries.
  
* **Populating Missing Values:** Used a self-join to fill missing **industry** values based on other entries with matching company names.
  
* **Standardizing Categories:** Consolidated variations (e.g., "Crypto Currency" and "CryptoCurrency") to a unified label ("Crypto").
  
* **Country Name Consistency:** Removed trailing punctuation from country names to ensure consistent entries.
  
* **Date Formatting:** Converted date strings to the SQL **DATE** type, facilitating better handling of time-based queries and analysis.

      -- Standardize "Crypto" category
      UPDATE data_staging2
      SET industry = 'Crypto'
      WHERE industry IN ('Crypto Currency', 'CryptoCurrency');

      -- Convert date strings to DATE type
      UPDATE data_staging2
      SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

      ALTER TABLE data_staging2
      MODIFY COLUMN `date` DATE;

**3. Removing Unnecessary Columns and Data**
To streamline the dataset for analysis:

* **Removing Irrelevant Rows:** Deleted records with no significant data (i.e., both **total_laid_off** and **percentage_laid_off** were null).
  
* **Dropping Helper Columns:** Removed the **row_num** column used temporarily for identifying duplicates.

      -- Delete rows with no useful data
      DELETE FROM data_staging2
      WHERE total_laid_off IS NULL
      AND percentage_laid_off IS NULL;

      -- Drop the helper column
      ALTER TABLE data_staging2
      DROP COLUMN row_num;

**Conclusion**

This project highlights critical SQL-based data cleaning techniques that can be applied to enhance the quality and reliability of data. By removing duplicates, standardizing formats, and addressing missing or inconsistent values, we produced a clean dataset suitable for data-driven analysis and reporting. These steps form a vital part of data preparation in any professional data analysis or business intelligence workflow.












