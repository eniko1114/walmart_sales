# Walmart Data Analysis: End-to-End SQL + Python Project P-9

## Project Overview

![Project Pipeline](https://github.com/najirh/Walmart_SQL_Python/blob/main/walmart_project-piplelines.png)


This project is an end-to-end data analysis solution designed to extract critical business insights from Walmart sales data. We utilize Python for data processing and analysis, SQL for advanced querying, and structured problem-solving techniques to solve key business questions. The project is ideal for data analysts looking to develop skills in data manipulation, SQL querying, and data pipeline creation.

---

## Project Steps

### 1. Set Up the Environment
   - **Tools Used**: Visual Studio Code (VS Code), Python, SQL (MySQL and PostgreSQL)
   - **Goal**: Create a structured workspace within VS Code and organize project folders for smooth development and data handling.

### 2. Set Up Kaggle API
   - **API Setup**: Obtain your Kaggle API token from [Kaggle](https://www.kaggle.com/) by navigating to your profile settings and downloading the JSON file.
   - **Configure Kaggle**: 
      - Place the downloaded `kaggle.json` file in your local `.kaggle` folder.
      - Use the command `kaggle datasets download -d <dataset-path>` to pull datasets directly into your project.

### 3. Download Walmart Sales Data
   - **Data Source**: Use the Kaggle API to download the Walmart sales datasets from Kaggle.
   - **Dataset Link**: [Walmart Sales Dataset](https://www.kaggle.com/najir0123/walmart-10k-sales-datasets)
   - **Storage**: Save the data in the `data/` folder for easy reference and access.

### 4. Install Required Libraries and Load Data
   - **Libraries**: Install necessary Python libraries using:
     ```bash
     pip install pandas numpy sqlalchemy mysql-connector-python psycopg2
     ```
   - **Loading Data**: Read the data into a Pandas DataFrame for initial analysis and transformations.

### 5. Explore the Data
   - **Goal**: Conduct an initial data exploration to understand data distribution, check column names, types, and identify potential issues.
   - **Analysis**: Use functions like `.info()`, `.describe()`, and `.head()` to get a quick overview of the data structure and statistics.

### 6. Data Cleaning
   - **Remove Duplicates**: Identify and remove duplicate entries to avoid skewed results.
   - **Handle Missing Values**: Drop rows or columns with missing values if they are insignificant; fill values where essential.
   - **Fix Data Types**: Ensure all columns have consistent data types (e.g., dates as `datetime`, prices as `float`).
   - **Currency Formatting**: Use `.replace()` to handle and format currency values for analysis.
   - **Validation**: Check for any remaining inconsistencies and verify the cleaned data.

### 7. Feature Engineering
   - **Create New Columns**: Calculate the `Total Amount` for each transaction by multiplying `unit_price` by `quantity` and adding this as a new column.
   - **Enhance Dataset**: Adding this calculated field will streamline further SQL analysis and aggregation tasks.

### 8. Load Data into MySQL and PostgreSQL
   - **Set Up Connections**: Connect to MySQL and PostgreSQL using `sqlalchemy` and load the cleaned data into each database.
   - **Table Creation**: Set up tables in both MySQL and PostgreSQL using Python SQLAlchemy to automate table creation and data insertion.
   - **Verification**: Run initial SQL queries to confirm that the data has been loaded accurately.

### 9. SQL Analysis: Complex Queries and Business Problem Solving
   - **Business Problem-Solving**: Write and execute complex SQL queries to answer critical business questions, such as:
     - Revenue trends across branches and categories.
     - Identifying best-selling product categories.
     - Sales performance by time, city, and payment method.
     - Analyzing peak sales periods and customer buying patterns.
     - Profit margin analysis by branch and category.
   - **Documentation**: Keep clear notes of each query's objective, approach, and results.

### 10. Project Publishing and Documentation
   - **Documentation**: Maintain well-structured documentation of the entire process in Markdown or a Jupyter Notebook.
   - **Project Publishing**: Publish the completed project on GitHub or any other version control platform, including:
     - The `README.md` file (this document).
     - Jupyter Notebooks (if applicable).
     - SQL query scripts.
     - Data files (if possible) or steps to access them.

---

## Requirements

- **Python 3.8+**
- **SQL Databases**: MySQL, PostgreSQL
- **Python Libraries**:
  - `pandas`, `numpy`, `sqlalchemy`, `mysql-connector-python`, `psycopg2`
- **Kaggle API Key** (for data downloading)

## Getting Started

1. Clone the repository:
   ```bash
   git clone <repo-url>
   ```
2. Install Python libraries:
   ```bash
   pip install -r requirements.txt
   ```
3. Set up your Kaggle API, download the data, and follow the steps to load and analyze.

---

## Project Structure

```plaintext
|-- data/                     # Raw data and transformed data
|-- sql_queries/              # SQL scripts for analysis and queries
|-- notebooks/                # Jupyter notebooks for Python analysis
|-- README.md                 # Project documentation
|-- requirements.txt          # List of required Python libraries
|-- main.py                   # Main script for loading, cleaning, and processing data
```
---

## Results and Insights

This section will include your analysis findings:
- **Sales Insights**: Key categories, branches with highest sales, and preferred payment methods.
- **Profitability**: Insights into the most profitable product categories and locations.
- **Customer Behavior**: Trends in ratings, payment preferences, and peak shopping hours.

## Future Enhancements

Possible extensions to this project:
- Integration with a dashboard tool (e.g., Power BI or Tableau) for interactive visualization.
- Additional data sources to enhance analysis depth.
- Automation of the data pipeline for real-time data ingestion and analysis.

---

## License

This project is licensed under the MIT License. 

---

## Acknowledgments

- **Data Source**: Kaggle’s Walmart Sales Dataset
- **Inspiration**: Walmart’s business case studies on sales and supply chain optimization.

---

```sq
SELECT * FROM walmart;

--DROP TABLE walmart;
--
SELECT COUNT(*) FROM walmart;

-- TOTAL PAYMENT METHODS AND TRANSACTIONS
SELECT 
     payment_method,
	 COUNT(*)

FROM walmart
GROUP BY payment_method

--
SELECT 
     -- COUNT(DISTINCT branch)
	 Branch
FROM walmart;

SELECT COUNT(DISTINCT Branch)
FROM walmart;
--100

SELECT MAX(quantity) FROM walmart;
SELECT MIN(quantity) FROM walmart;
--
--- Business Problems:--------------------------------------------------------------------

-- 1. What are the different payment methods, and how many transactions and
-- items were sold with each method?

SELECT 
     payment_method,
	 COUNT(*) as no_payments,
	 SUM(quantity) as no_qty_sold

FROM walmart
GROUP BY payment_method

-- 2. Which category received the highest average rating in each branch? Display branch, category, avg rating

SELECT * 
FROM
(  SELECT
      branch,
      category,
      AVG(rating) as avg_rating,
	  RANK() OVER(PARTITION BY branch ORDER BY AVG(rating) DESC) as rank
   FROM walmart
   GROUP BY 1,2
)
WHERE rank = 1

-- 3. Determine the Busiest Day for Each Branch: based on transaction volume
-- Problem: date column is text not date format

SELECT 
     date,
	 TO_DATE(date, 'DD/MM/YY') as formatted_date
FROM walmart

SELECT 
     date,
	 TO_CHAR(TO_DATE(date, 'DD/MM/YY'), 'Day') as day_name
FROM walmart

SELECT 
     branch,
	 TO_CHAR(TO_DATE(date, 'DD/MM/YY'), 'Day') as day_name,
	 COUNT(*) as no_transactions
FROM walmart
GROUP BY 1,2
ORDER BY 1, 3 DESC

SELECT *
FROM
   (SELECT
       branch,
	   TO_CHAR(TO_DATE(date, 'DD/MM/YY'), 'Day') as day_name,
	   COUNT(*) as no_transactions,
	   RANK() OVER(PARTITION BY branch ORDER BY COUNT(*) DESC) as rank 
     FROM walmart
     GROUP BY 1,2
   )
WHERE rank = 1

-- 4. Calculate Total Quantity Sold by Payment Method

SELECT 
     payment_method,
     SUM(quantity) as no_qty_sold
FROM walmart
GROUP BY payment_method

-- 5. Analyze Category Ratings by City (1h 09 mins) List the city, avr rating, min rating and max rating

SELECT
     city,
	 category,
	 MIN(rating) as min_rating,
	 MAX(rating) as max_rating,
	 AVG(rating) as avg_rating
FROM walmart
GROUP BY 1, 2

-- 6. Calculate Total Profit by Category: : What is the total profit for each category (unit_price*quantity*profit_margin),
-- ranked from highest to lowest?

SELECT
      category,
	  SUM(total) as total_revenue,
	  SUM(total * profit_margin) as profit
FROM walmart
GROUP BY 1

-- 7. Determine the Most Common Payment Method per Branc:
-- What is the most frequently used payment method in each branch
-- 1.group by branch then group by payment method then count on invoice id
-- cte is Common Table Expression (CTE)  It's like creating a "temporary table"
-- for intermediate results, used only within the query.

WITH cte
AS
(SELECT 
      branch,
	  payment_method,
	  COUNT(*) as total_transaction,
	  RANK() OVER(PARTITION BY branch ORDER BY COUNT(*) DESC) as rank
FROM walmart
GROUP BY 1, 2
)
SELECT *
FROM cte
WHERE rank = 1 

-- 8.Analyze Sales Shifts Throughout the Day: How many transactions occur in each shift
-- (Morning, Afternoon, Evening) across branches?
-- based on time column categorize each order (first convert to time)

SELECT
*,
time::time
FROM walmart;
-- new column is created at the end from the time column called time without timezone

SELECT
*,
    CASE 
         WHEN EXTRACT(HOUR FROM (time::time)) < 12 THEN 'Morning'
		 WHEN EXTRACT(HOUR FROM (time::time)) BETWEEN 12 AND 17 THEN 'Afternoon'
		 ELSE 'Evening'
    END day_time
FROM walmart
-- NEW COLUMN IS CREATED AT THE END


SELECT
CASE 
         WHEN EXTRACT(HOUR FROM (time::time)) < 12 THEN 'Morning'
		 WHEN EXTRACT(HOUR FROM (time::time)) BETWEEN 12 AND 17 THEN 'Afternoon'
		 ELSE 'Evening'
    END day_time,
	COUNT(*)
FROM walmart
GROUP BY 1 
-- AFTERNOON has the highest number of count mostly

SELECT
      branch,
CASE 
         WHEN EXTRACT(HOUR FROM (time::time)) < 12 THEN 'Morning'
		 WHEN EXTRACT(HOUR FROM (time::time)) BETWEEN 12 AND 17 THEN 'Afternoon'
		 ELSE 'Evening'
    END day_time,
	COUNT(*)
FROM walmart
GROUP BY 1, 2
ORDER BY 1, 3 DESC


-- 9. Identify 5 Branches with Highest Revenue Decline Year-Over-Year: Which branches experienced
-- the largest decrease in revenue compared to the previous year? (current year 2023 and last year 2022)
-- rdr == last_rev-cr_rev/ls_rev*100

SELECT 
     branch,
	 SUM(total) as revenue
FROM walmart
GROUP BY 1 
-- this shows each branch and the total revenue

SELECT *,
EXTRACT (YEAR FROM TO_DATE(date, 'DD/MM/YY')) as formatted_date
FROM walmart

-- THE WHOLE CODE:

-- 2022 SALES
WITH revenue_2022
AS
(
    SELECT 
       branch,
	   SUM(total) as revenue
   FROM walmart
   WHERE EXTRACT (YEAR FROM TO_DATE(date, 'DD/MM/YY')) = 2022
   GROUP BY 1 
),

revenue_2023
AS
(
 SELECT 
       branch,
	   SUM(total) as revenue
   FROM walmart
   WHERE EXTRACT (YEAR FROM TO_DATE(date, 'DD/MM/YY')) = 2023
   GROUP BY 1
)

SELECT
      ls.branch,
	  ls.revenue as last_year_revenue,
	  cs.revenue as current_year_revenue
FROM revenue_2022 as ls
JOIN
revenue_2023 as cs 
ON ls.branch = cs.branch 

-- ls: last year revenue, cs:current year revenue
-- we only want to see where the revenue decreased


WITH revenue_2022
AS
(
    SELECT 
       branch,
	   SUM(total) as revenue
   FROM walmart
   WHERE EXTRACT (YEAR FROM TO_DATE(date, 'DD/MM/YY')) = 2022
   GROUP BY 1 
),

revenue_2023
AS
(
 SELECT 
       branch,
	   SUM(total) as revenue
   FROM walmart
   WHERE EXTRACT (YEAR FROM TO_DATE(date, 'DD/MM/YY')) = 2023
   GROUP BY 1
)

SELECT
      ls.branch,
	  ls.revenue as last_year_revenue,
	  cs.revenue as current_year_revenue
FROM revenue_2022 as ls
JOIN
revenue_2023 as cs 
ON ls.branch = cs.branch 
WHERE 
    ls.revenue > cs.revenue

-- 42 branch where the revenue has decreased

WITH revenue_2022
AS
(
    SELECT 
       branch,
	   SUM(total) as revenue
   FROM walmart
   WHERE EXTRACT (YEAR FROM TO_DATE(date, 'DD/MM/YY')) = 2022
   GROUP BY 1 
),

revenue_2023
AS
(
 SELECT 
       branch,
	   SUM(total) as revenue
   FROM walmart
   WHERE EXTRACT (YEAR FROM TO_DATE(date, 'DD/MM/YY')) = 2023
   GROUP BY 1
)

SELECT
      ls.branch,
	  ls.revenue as last_year_revenue,
	  cs.revenue as current_year_revenue,
	  ROUND((ls.revenue - cs.revenue)::numeric/
	  ls.revenue::numeric * 100,
	  2) as rev_decrease_ratio 
FROM revenue_2022 as ls
JOIN
revenue_2023 as cs 
ON ls.branch = cs.branch 
WHERE 
    ls.revenue > cs.revenue
ORDER BY 4 DESC
LIMIT 5
-- HIGHEST IS WALM045, SELECT TOP 5 where the revenue decrease is the highest
``` 








