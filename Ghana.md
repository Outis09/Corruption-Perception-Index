What was Ghana's CPI in 2021?
------
**Query:**

```sql
SELECT country,
       score
  FROM active
 WHERE country = 'Ghana'
  AND year = '2021';
```

**Results:**
|country|score|
|--------|------|
|Ghana|43|

* In 2021, Ghana's CPI was 43.

How does this compare with the rest of the world in terms of rank and average?
------
**Rank**

**Query:**

```sql
SELECT country,
       score,
       rank
  FROM active
 WHERE country = 'Ghana'
  AND year = '2021';
```

**Results:**
|country|score|rank|
|--------|-----|---|
|Ghana|43|73|

* With a score of 43, Ghana ranked 73rd in the world.

**Average**
**Query:**
```sql
WITH 
--gets average score for the world in 2021
avg_score as (
	SELECT round(avg(score),2) as avg_score
          FROM active
         WHERE year = '2021'
	 ),
--gets Ghana's score for 2021
gh_score as (
	SELECT score as gh_score
          FROM active
         WHERE country = 'Ghana' AND year = '2021')
	 
SELECT gh_score,
       avg_score
  FROM gh_score, avg_score;
```

**Results:**
|gh_score|avg_score|
|---------|--------|
|43|43.27|

* Ghana's CPI in 2021 is around the world average

How does the score compare with Africa in terms of rank and average?
-----
**Rank:**
**Query:**
```sql
--gets the score of African countries
WITH african_countries as (
	SELECT country,
	       score,
	       year
	 FROM  active
	 WHERE region = 'SSA' 
            OR country IN ('Egypt','Algeria','Morocco','Libya','Tunisia') )
    )

SELECT country,
       score,
       RANK() OVER(ORDER BY score DESC)
  FROM african_countries
 WHERE year='2021';
```

**Results:**
|country|score|rank|
|---------|-----|-----|
Seychelles|70|1|
Cabo Verde|58|2|
Botswana|55|3
Mauritius|54|4
Rwanda|53|5
Namibia|49|6
Sao Tome and Principe|45|7
South Africa|44|8
Tunisia|44|8
Senegal|43|10
|Ghana|43|10|

* Ghana ranked joint 10th in Africa.

**Average:**
**Query:**
```sql
WITH 
--gets average score of African countries for 2021
avg_score as (
	SELECT round(avg(score),2) as avg_cpi_africa
	  FROM active
	 WHERE year = '2021'
	   AND (region = 'SSA' OR country IN ('Egypt','Tunisia','Morocco','Libya','Algeria'))
	   ),
--gets Ghana's score for 2021
gh_score as (
	SELECT score as gh_score
	  FROM active
	 WHERE country = 'Ghana' AND year = '2021'
	 )
	 
SELECT gh_score,
       avg_score
  FROM gh_score, avg_score;
```

**Results:**
|gh_score|avg_cpi_africa|
|--------|--------------|
|43|32.57|

* Ghana's CPI in 2021 is above the average CPI in Africa

How does the score compare in West Africa?
---

```sql
SELECT country,
       score,
       RANK() OVER(ORDER BY score DESC)
  FROM active
 WHERE country IN ('Benin','Burkina Faso','Cape Verde', 
                   'Cote d''Ivoire','Gambia','Ghana','Guinea','Guinea Bissau',
		   'Liberia','Mali','Niger','Nigeria','Senegal','Sierra Leone','Togo')
   AND year = '2021';
```

**Results:**
|country|score|rank|
|-----|-------|---------|
|Senegal|43|1|
|Ghana|43|1|
|Benin|42|3|
|Burkina Faso|42|3|
|Gambia|37|5|
|Cote d'Ivoire|36|6|
|Sierra Leone|34|7|
|Niger|31|8|
|Togo|30|9|
|Mali|29|10|
|Liberia|29|10|
|Guinea|25|12|
|Nigeria|24|13|

** Ghana ranked joint first in West Africa

How has the score and ranks changed from the past year?
**World**
**Query:**
```sql
WITH
--gets Ghana's score and rank for 2020
prev_year as (
	SELECT score as score_2020,
	       rank as rank_2020
	  FROM active
	 WHERE country='Ghana'
	   AND year = '2020'
	   ),
--gets Ghana's score and rank for 2021
curr_year as (
	SELECT score as score_2021,
	       rank as rank_2021
	  FROM active
	 WHERE country = 'Ghana'
	   AND year = '2021'
	   )

SELECT score_2020,
       rank_2020,
       score_2021,
       rank_2021,
       score_2021 - score_2020 as score_change,
       rank_2021 - rank_2020 as rank_change
  FROM prev_year, curr_year;
```

**Results:**
|score_2020|rank_2020|score_2021|rank_2021|score_change|rank_change|
|-----------|---------|---------|-------|--------|------------|
|43|75|43|73|0|-2|

* Ghana's score in 2021 is the same score from 2020 however Ghana's ranked increased by two positions. That is,from 75 in 2020 to 73 in 2021. What accounts for this? Did the world average drop in 2021? Let's take a look.

**Query:**
```sql
WITH 
--gets the world average for 2020
prev_avg as (
	SELECT round(avg(score),2) as avg_2020
          FROM active
          WHERE year = '2020'
	  ),
--gets world average for 2021
curr_avg as (
	SELECT round(avg(score),2) as avg_2021
          FROM active
         WHERE year = '2021'
	 )
	 
SELECT avg_2020,
       avg_2021,
       avg_2021 - avg_2020 as change
  FROM prev_avg,curr_avg;
```

**Results:**
|avg_2020|avg_2021|-0.07|
|--------|--------|-----|
|43.34|43.27|-0.07|

* The global average did reduce by 0.07 from 2020 so the no change in score but change in rank can be attributed to this.

* It could also be that even through the CPI remianed the same in Ghana over the two years, the CPI of any of the 74 countries ahead of Ghana in 2020 dropped in 2021 hence why Ghana's position increased.

Let's compare 2021 rank to 2020 rank in Africa.
**Query:**
```sql
WITH 
--ranks scores of African countries in 2021
ranks_2021 as (
	SELECT country,
	       score,
	       RANK() OVER (ORDER BY score DESC) as rank_2021
	  FROM active
	 WHERE year = '2021'
	   AND (region = 'SSA' 
	    OR country IN ('Egypt','Algeria','Morocco','Libya','Tunisia') )
	    ),
--ranks scores of African countries for 2020
ranks_2020 as (
	SELECT country,
	       score,
	       RANK() OVER (ORDER BY score DESC) as rank_2020
	  FROM active
	 WHERE year = '2020'
	   AND (region = 'SSA' 
	    OR country IN ('Egypt','Algeria','Morocco','Libya','Tunisia') )
	    )

SELECT rank_2020,
       rank_2021
  FROM ranks_2020,ranks_2021
 WHERE ranks_2020.country ='Ghana' and ranks_2021.country='Ghana';
```

**Results:**
|rank_2020|rank_2021|
|---------|---------|
|11|10|

* Ghana's rank in Africa increased by 1 position from 2020. However, this is not because our score increased but rather because Senegal's CPI dropped in 2021.

Ghana ranked joint 1st with Senegal in West Africa in 2021 so I will check the ranks in West Africa in 2020 to prove the statement above.
**Query:**
```sql
SELECT country,
       score,
       RANK() OVER(ORDER BY score DESC)
  FROM active
 WHERE country IN ('Benin','Burkina Faso','Cape Verde', 'Cote d''Ivoire',
                    'Gambia','Ghana','Guinea','Guinea Bissau','Liberia','Mali',
		    'Niger','Nigeria','Senegal','Sierra Leone','Togo')
   AND year = '2020'
 LIMIT 5;
```

**Results:**
|country|score|rank|
|-------|-----|----|
|Senegal|45|1|
|Ghana|43|2|
|Benin|41|3|
|Burkina Faso|40|4|
|Gambia|37|5|

With the same score of 43 in 2020, Ghana ranked 2nd in West Africa. Senegal ranked first in West Africa. This goes to prove that Ghana's first position in West Africa and the position increase in Africa in 2021 is as a result of the scores dropping in other countries.

Over the last decade, what has been Ghana's best performing years in terms of CPI
-----
**Best Year**
**Query:**
```sql
SELECT country,
       score,
       year 
  FROM active
 WHERE country = 'Ghana'
   AND score = (SELECT max(score) FROM active WHERE country = 'Ghana');
```

**Results:**
|country|score|year|
|----------|-------|----------|
|Ghana|48|2014|

* Ghana's best performing year in terms of CPI is 2014 with a score of 48
**Worst year**
**Query:**
```sql
SELECT country,
       score,
       year 
  FROM active
 WHERE country = 'Ghana'
   AND score = (SELECT min(score) FROM active WHERE country = 'Ghana');
```

**Results:**
|country|score|year|
|-------|-----|----|
|Ghana|40|2017|

* Ghana's worst year in terms of CPI is 2017 with a score of 40.

Two governments have undergone one term government in the last decade. Ex Prez. John Dramani's government from 2013 to 2016 and H.E. Nana Addo Dankwa Akuffo Addo's government from 2017 to 2020.

I will compare the average CPI of the four years each government was in power in the last decade.

**Query:**
```sql
WITH 
--gets scores from 2017 to 2020
nadaa_govt as (
	SELECT score as nadaa_scores,
	       year
	  FROM active
	 WHERE country = 'Ghana'
	   AND year::int BETWEEN 2017 AND 2020
	   ),
--gets scores from 2013 to 2016
jdm_govt as (
	SELECT score as jdm_scores,
	       year
	  FROM active
	 WHERE country = 'Ghana'
	   AND year::int BETWEEN 2013 and 2016
	   )
	   
SELECT avg(nadaa_scores) as nadaa_avg ,
       avg(jdm_scores) as jdm_avg
  FROM nadaa_govt,jdm_govt;
```

**Results:**
|nadaa_avg|jdm_avg|
|---------|-------|
|41.25|46|

* In comparison the government of John Dramani Mahama did better than the government of Nana Akuffo Addo in terms of CPI.

Trend over the past 10 years?
**Query:**
```sql
  SELECT year,
         score
    FROM active
   WHERE country = 'Ghana'
ORDER BY year; 
```

**Results:**
|year|score|
|----|-----|
|2012|45|
|2013|46|
|2014|48|
|2015|47|
|2016|43|
|2017|40|
|2018|41|
|2019|41|
|2020|43|
|2021|43|


