# SQL Data Cleaning: Ensuring Consistency and Reliability in Business Datasets

**Overview**

This project focuses on professional data cleaning practices using SQL to prepare a dataset containing key business metrics for accurate analysis. By implementing SQL techniques to eliminate inconsistencies, standardize entries, and resolve missing values, this process results in a uniform and reliable dataset, optimized for analytical and reporting needs. These data-cleaning steps ensure accuracy, enhancing data quality for high-level decision-making.

**Table of Contents**

* Project Overview
* Dataset Description
* Data Cleaning Process
  * Removing Duplicates
  * Standardizing Data and Fixing Errors
  * Eliminating Irrelevant Data
* Conclusion

**Project Overview**

This project addresses common data quality issues, such as duplicate records, missing values, and inconsistent formatting. Through a series of SQL-based cleaning and standardization steps, we validated and refined the dataset to produce a consistent and error-free resource for deeper analysis, reporting, or integration into business intelligence workflows.

**Dataset Description**

The dataset includes the following key attributes:

* **company:** Company name

* **location:** Company location

* **industry:** Industry category
* **total_laid_off:** Number of employees laid off
* **percentage_laid_off:** Percent of workforce affected
* **date:** Date of the event
* **stage:** Company stage (e.g., startup, established)
* **country:** Country of operation
* **funds_raised_millions:** Total funds raised by the company (in millions)

**Data Cleaning Process**

**1. Removing Duplicates**

To ensure the uniqueness of each entry:

* **Staging Table:** Created a temporary staging table to preserve the original data.

* **Identifying Duplicates:** Used SQL's **ROW_NUMBER()** with **PARTITION BY** to identify duplicate entries based on key fields like company name, location, and date.

* **Eliminating Redundant Records:** Removed duplicate rows, keeping only the first instance of each entry.

      -- Identify and mark duplicates
      WITH duplicate_cte AS (
          SELECT *,
             ROW_NUMBER() OVER (
                  PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, date, stage, country, funds_raised_millions
              ) AS row_num
          FROM data_staging
      )
      -- Delete duplicates in the final table
      DELETE FROM data_staging2
      WHERE row_num > 1;

**2. Standardizing Data and Correcting Errors**

To ensure consistency and accuracy:

* **Null Handling:** Replaced empty fields with **NULL** values to simplify downstream analysis.

* **Populating Missing Data:** Used a self-join to complete missing industry values based on the company name.

* **Category Standardization:** Unified variations (e.g., “Crypto Currency” and “CryptoCurrency”) into a single label (“Crypto”).

* **Country Name Consistency:** Removed trailing punctuation from country names to maintain uniformity.

* **Date Formatting:** Converted date entries into SQL **DATE** format to support time-based analysis.

      -- Standardize "Crypto" category
      UPDATE data_staging2
      SET industry = 'Crypto'
      WHERE industry IN ('Crypto Currency', 'CryptoCurrency');

      -- Convert date strings to DATE type
      UPDATE data_staging2
      SET date = STR_TO_DATE(date, '%m/%d/%Y');

      ALTER TABLE data_staging2
      MODIFY COLUMN date DATE;

**3. Removing Unnecessary Columns and Data**

To streamline the dataset:

* **Removing Irrelevant Rows:** Deleted records with null values for both **total_laid_off** and **percentage_laid_off**, as these provided no meaningful data.

* **Dropping Temporary Columns:** Removed the helper column **row_num** used for identifying duplicates.

      -- Delete rows with no useful data
      DELETE FROM data_staging2
      WHERE total_laid_off IS NULL
      AND percentage_laid_off IS NULL;

      -- Drop the helper column
      ALTER TABLE data_staging2
      DROP COLUMN row_num;

**Conclusion**

This project showcases critical SQL-based data cleaning techniques that enhance data quality, reliability, and usability. By systematically removing duplicates, standardizing data formats, and addressing missing values, we prepared a clean and consistent dataset suitable for analysis and business intelligence purposes, providing a robust foundation for data-driven decisions.











