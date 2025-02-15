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
STEP 1

~~~sql
WITH fixed AS (
SELECT company, LEFT(RIGHT(valuation, LENGTH(valuation) - 1), LENGTH(RIGHT(valuation, LENGTH(valuation) - 1)) - 1)::"numeric" * 1000000000 AS valuation,
EXTRACT(YEAR FROM date_joined::date) AS date_joined, industry, city, country, continent, year_founding::"numeric" AS year_founding,
CASE 
	WHEN funding = 'Unknown' THEN 0
	WHEN funding LIKE '%B%' THEN LEFT(RIGHT(funding, LENGTH(funding) - 1), LENGTH(RIGHT(funding, LENGTH(funding) - 1)) - 1)::"numeric" * 1000000000
	WHEN funding LIKE '%M%' THEN LEFT(RIGHT(funding, LENGTH(funding) - 1), LENGTH(RIGHT(funding, LENGTH(funding) - 1)) - 1)::"numeric" * 100000000
END AS funding
FROM unicors
)
~~~
