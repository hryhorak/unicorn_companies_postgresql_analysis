# Unicorn Companies

Private companies with a valuation over $1 billion as of March 2022, including each company's current valuation, funding, country of origin, industry, select investors, and the years they were founded and became unicorns.

## Data Definition:
- Company – Company Name
- Valuation – Company Valuation
- Date Joined – Date Company Achieved Unicorn Status
- Industry – Industry
- City – Headquarters City
- Country – Headquarters Country
- Continent – Headquarters Continent
- Year Founded – Year of Company Foundation
- Funding – Total Funding Raised

---
## STEP 1
Icluding import .csv file and connect it to database in pgAdmin4.

## STEP 2
This step includes cleaning, formatting and preparing the data for analysis. All the columns had a text data type, so I used the appropriate functions to change them to the appropriate ones.

~~~sql
WITH fixed AS (
SELECT company,
-- remove **$** sign and **B**, converting to a numeric data type and multiplying by 1,000,000,000. 
LEFT(RIGHT(valuation, LENGTH(valuation) - 1), LENGTH(RIGHT(valuation, LENGTH(valuation) - 1)) - 1)::"numeric" * 1000000000 AS valuation,
EXTRACT(YEAR FROM date_joined::date) AS date_joined, industry, city, country, continent, year_founding::"numeric" AS year_founding,
-- column contained unknown, so I replaced it with 0, removed the $ and B/M, converted to a numeric data type, and multiplied by a million or a billion respectively.
CASE 
	WHEN funding = 'Unknown' THEN 0
	WHEN funding LIKE '%B%' THEN LEFT(RIGHT(funding, LENGTH(funding) - 1), LENGTH(RIGHT(funding, LENGTH(funding) - 1)) - 1)::"numeric" * 1000000000
	WHEN funding LIKE '%M%' THEN LEFT(RIGHT(funding, LENGTH(funding) - 1), LENGTH(RIGHT(funding, LENGTH(funding) - 1)) - 1)::"numeric" * 100000000
END AS funding
FROM unicors
)
~~~

## STEP 3
After data preparation, analysis can be performed. The first question: Which unicorn companies have had the biggest return on investment in %?

~~~sql
SELECT company,
CASE WHEN funding = 0 OR valuation = 0 THEN 0
	ELSE ROUND(valuation / funding * 100, 0) END AS "investment_return(in %)"
FROM fixed
ORDER BY "investment_return(in %)" DESC 
LIMIT 10;
~~~
