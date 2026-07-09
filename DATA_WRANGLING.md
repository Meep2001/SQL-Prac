Data wrangling (also called data munging) is the process of cleaning, transforming, and preparing raw data into a format that is suitable for analysis or use.


LEFT --> remove certain number of characters from left of the string
RIGHT --> remove certain number of characters from right of the string
LENGTH --> gives length of the string

For left() and right() is the data type of the function is not string its converted to string and then
the left or applied

SELECT incidnt_num,
       date,
       LEFT(date, 10) AS cleaned_date,
       RIGHT(date, 17) AS cleaned_time
  FROM tutorial.sf_crime_incidents_2014_01

TRIM
The TRIM function is used to remove characters from the beginning and end of a string.

SELECT location,
       TRIM(both '()' FROM location)
  FROM tutorial.sf_crime_incidents_2014_01

The TRIM function takes 3 arguments. 

    First, you have to specify whether you want to remove characters from the beginning ('leading'), the end ('trailing'), or both ('both', as used above). 

    Next you must specify all characters to be trimmed. 
    Any characters included in the single quotes will be removed from both beginning, end, or both sides of the string. 

    Finally, you must specify the text you want to trim using FROM.

POSITION
Returns the position of a string in another from left

SELECT incidnt_num,
       descript,
       POSITION('A' IN descript) AS a_position
  FROM tutorial.sf_crime_incidents_2014_01

STRPOS

You can also use the STRPOS function to achieve the same results—just replace IN with a comma and switch the order of the string and substring:

SELECT incidnt_num,
       descript,
       STRPOS(descript, 'A') AS a_position
  FROM tutorial.sf_crime_incidents_2014_01

Note:
 POSITION and STRPOS functions are case-sensitive.

SUBSTR
LEFT and RIGHT both create substrings of a specified length, but they only do so starting from the sides of an existing string. If you want to start in the middle of a string, you can use SUBSTR. The syntax is SUBSTR(*string*, *starting character position*, *# of characters*):

SELECT incidnt_num,
       date,
       SUBSTR(date, 4, 2) AS day
  FROM tutorial.sf_crime_incidents_2014_01

CONCAT
You can combine strings from several columns together (and with hard-coded values) using CONCAT. Simply order the values you want to concatenate and separate them with commas. If you want to hard-code values, enclose them in single quotes. Here's an example:

SELECT incidnt_num,
       day_of_week,
       LEFT(date, 10) AS cleaned_date,
       CONCAT(day_of_week, ', ', LEFT(date, 10)) AS day_and_date
  FROM tutorial.sf_crime_incidents_2014_01

Alternatively, you can use two pipe characters (||) to perform the same concatenation:

SELECT incidnt_num,
       day_of_week,
       LEFT(date, 10) AS cleaned_date,
       day_of_week || ', ' || LEFT(date, 10) AS day_and_date
  FROM tutorial.sf_crime_incidents_2014_01

UPPER and LOWER

SELECT incidnt_num,
       address,
       UPPER(address) AS address_upper,
       LOWER(address) AS address_lower
  FROM tutorial.sf_crime_incidents_2014_01

Turning strings into dates

Date in DB as '01/31/2014 08:00:00 AM +0000' --> string 

SELECT incidnt_num,
       date,
       (SUBSTR(date, 7, 4) || '-' || LEFT(date, 2) ||
        '-' || SUBSTR(date, 4, 2))::date AS cleaned_date
  FROM tutorial.sf_crime_incidents_2014_01 

Turning dates into more useful dates
 if you want to deconstruct a date you can use EXTRACT to pull the pieces apart one-by-one:

 SELECT cleaned_date,
       EXTRACT('year'   FROM cleaned_date) AS year,
       EXTRACT('month'  FROM cleaned_date) AS month,
       EXTRACT('day'    FROM cleaned_date) AS day,
       EXTRACT('hour'   FROM cleaned_date) AS hour,
       EXTRACT('minute' FROM cleaned_date) AS minute,
       EXTRACT('second' FROM cleaned_date) AS second,
       EXTRACT('decade' FROM cleaned_date) AS decade,
       EXTRACT('dow'    FROM cleaned_date) AS day_of_week
  FROM tutorial.sf_crime_incidents_cleandate

Note: DATETRUNC Funtion do on own--> truncate the date to the nearest precision you want eg.
hen you DATE_TRUNC by year, any value in that year will be listed as January 1st of that year

COALESCE
Occasionally, you will end up with a dataset that has some nulls that you'd prefer to contain actual values. This happens frequently in numerical data (displaying nulls as 0 is often preferable), and when performing outer joins that result in some unmatched rows. In cases like this, you can use COALESCE to replace the null values:

SELECT incidnt_num,
       descript,
       COALESCE(descript, 'No Description')
  FROM tutorial.sf_crime_incidents_cleandate
 ORDER BY descript DESC