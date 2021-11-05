## Case Study:  How does a Bike-Share navigate Speedy Success?  
---
**Data Analyst:** *Joe Szymczak-Parker*  
**Client/Sponsor:** *Cyclistic Bike-Share*

___

**Background:**  
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.  

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members.

___

**Project Purpose:**  
Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Although the pricing flexibility helps Cyclistic attract more customers, Moreno, the Director of Marketing and my manager, believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a very good chance to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.

___

**Moreno has set a clear goal:**  
Design marketing strategies aimed at converting casual riders into annual members. In order to do that, however, the marketing analyst team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. Moreno and her team are interested in analyzing the Cyclistic historical bike trip data to identify trends

___

**Scope / Major Project Activities:**

* How do annual members and casual riders use Cyclistic bikes differently?
* Why would casual riders buy Cyclistic annual memberships?
* How to use digital media to influence casual riders to become members?

___

**Not included:**    

Moreno has assigned me, the junior Data Analyst, to answer the first solely the first question:
##### **How do annual members and casual riders use Cyclistic bikes differently?**    

Though the other questions are in scope for our company as a whole, they are out of scope for this analysis.

___

**Deliverables:**

  * A clear statement of the business task
  * A description of all data sources used
  * Documentation of any cleaning or manipulation of data
  * A summary of my analysis
  * Supporting visualizations and key findings
  * My top three recommendations based on my analysis

___

**Schedule Overview / Major Milestones:**
~~~SQL
Day 1 ( Ask & Prepare ):  
    * Identify Business Task, consider Stakeholder Needs, Identify, Organize,  
    and Verify the data’s Integrity and explore boundaries of data for use  
    in Business Task.  

Day 2 ( Process ):  
    * Clean & Process data for analysis.  

Day 3 ( Analyze ):
    * Identify trends and relationships.

Day 4 ( Share ):
    * Create effective visualizations.

Day 5 ( Act ):
    * Prepare presentation of insights
  ~~~


*Estimated date for completion:
This is my “if all goes well and I have everything I need, this is when I’ll be done” date.

___

## Deliverable 1:
#### Clear Statement of Business Task

  * How do annual members and casual riders use Cyclistic bikes differently?

___

## Deliverable 2:
#### Description of all Data Sources  

For the purposes of this case study, the datasets exhibit a different name because Cyclistic is a fictional company. However, the data itself comes from a real world ride share company and has been made available by Motivate International Inc. under this [License](https://www.divvybikes.com/data-license-agreement/ "Motivate International License").    

The trip data can be found @ [Divvy Tripdata](https://divvy-tripdata.s3.amazonaws.com/index.html "Divvy Tripdata"). I have downloaded it in the form of 12 excel spreadsheets delineated by month starting on 2020-08 and ending with 2021-07.  This spans 1 year of data separated into the following data types:


~~~ SQL
CREATE TABLE IF NOT EXISTS rides (
	id SERIAL,
	ride_id VARCHAR(50),               -- Unique Ride ID (approximately 5 million)
	rideable_type VARCHAR(50),         -- Docked, Classic, & Electric Bike Categories
	started_at TIMESTAMP,              -- Timestamp @ start of unique ride
	ended_at TIMESTAMP,                -- Timestamp @ end of unique ride
	start_station_name VARCHAR(250),   -- String of Start Station Name
	start_station_id VARCHAR(250),     -- Alphanumeric Station ID
	end_station VARCHAR(250),          -- String of End Station Name
	end_station_id VARCHAR(250),       -- Alphanumeric Station ID
	start_lat REAL,                    -- Floating Point Decimal Start Latitude
	start_lng REAL,                    -- Floating Point Decimal Start Longitude
	end_lat REAL,                      -- Floating Point Decimal End Latitude
	end_lng REAL,                      -- Floating Point Decimal End Longitude
	member_casual VARCHAR(50),         -- Member or Casual Categories
	ride_length_days INT,              -- Integer Type:  Length of Ride (Days)
	ride_length_hmmss TIME,            -- Time Type:  Length of Ride (Hours:Minutes:Seconds)
	day_of_week INT,                   -- Day of Week 1 = Sunday through 7 = Saturday
	PRIMARY KEY (id)
    );
~~~

---

## Deliverable 3: Change Log
#### Documentation of any cleaning or manipulation of data

### New
	-Added column "num_of_days" = Col(ended_at)-Col(started_at) & formatted to track patterns  in longer length of rides
	-Added column "ride_length" = Col(ended_at)-Col(started_at) & formatted to h:mm:ss to track patterns in length of rides
	-Added column "day_of_week" = WEEKDAY(cell,1) to track day of week for ride start times


### Changes  
	- ride_id:  Identified non-unique and/or reformattable outlier IDs that didn't conform  to the usual 16 alphanumeric characters
		using =LEN(A:A)
	- rideable_type: filter verified boolean nature of docked or electric bike (note: new "classic bike" type began in 5th month)
	-started_at:  Changed date format to YYYY-MM-DD h:mm:ss
	-ended_at:  Changed date format to YYYY-MM-DD h:mm:ss
	-start_lat:  Changed format to 00.000000 for consistent resolution
	-start_lng:  Changed format to 00.000000 for consistent resolution
	-end_lat:  Changed format to 00.000000 for consistent resolution
	-end_lng:  Changed format to 00.000000 for consistent resolution
	-member_casual:  filter verified boolean nature of casual or member

### Fixes
	-deleted non-unique and/or reformattable outlier ride_id that didn't conform to the usual 16 alphanumeric characters
	-deleted rows that contained testing data in start_station_name or end_station_name rows:
		-HUBBARD ST BIKE CHECKING (LBS-WH-TEST)
		-WATSON TESTING - DIVVY
		-New alphanumeric station_name began in 2020-12
		-Test sites disappeared in 2021-01 and came back in 2021-05
	-deleted rows where the end datetime began before the start datetime

___

## Deliverable 4:
#### A summary of my analysis

After cleaning the data in Microsoft Excel according to the Change Log presented in the previous deliverable, I created a "*rides*" database in PostGreSQL so that I could analyze patterns in the data.  Each spreadsheet (12 total) was added to the "*rides*" table with the following code:

~~~ SQL
COPY rides (
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	start_station_id,
	end_station,
	end_station_id,
	start_lat,
	start_lng,
	end_lat,
	end_lng,
	member_casual,
	ride_length_days,
	ride_length_hmmss,
	day_of_week
	)
FROM        --This is a local folder structure
	'C:\Users\Public\Documents\postgresql\202008-divvy-tripdata.csv'
DELIMITER   --Each file was from a different month
	','
CSV
	HEADER;
~~~

After the rides table was created in PostGreSQL, it contained nearly 5 million unique data points!   

Since my business task was to identify use patterns in casual users vs. full members, I determined that the data would allow the following dimensions for analysis:

1.) Membership Comparisons (over the entire year)    

* Casual Riders vs Membership Riders   
* Bike Types:  Docked vs Classic vs Electric  

      * AVG(), MIN(), MAX(), MODE(), Median

2.) Time Comparisons
  * What are month to month patterns in use between the user types?
  * What days of the week do each user type ride bikes?
  * How do each user types use the bikes seasonally?

___

Query for Question #1 (Membership Comparisons:  Casual vs Member)
~~~ SQL
-- Average, Max, Mode, & Median ride time casual vs member
-- Note: DATE_TRUNC is how you round intervals in PostGreSQL
-- Note: No Median function in PostGreSQL so provided replacement below

SELECT
    member_casual,
    DATE_TRUNC('second', AVG(ended_at - started_at)) AS avg_ride_length,                        -- Avg Ride Length - Note: DATE_TRUNC is how you round intervals in PostGreSQL
    MIN(ended_at - started_at) AS min_ride_length,                                              -- Min Ride Length
    MAX(ended_at - started_at) AS max_ride_length,                                              -- Max Ride Length
    MODE() WITHIN GROUP (ORDER BY ended_at - started_at) AS mode_ride_length,                   -- Mode Ride Length
    percentile_cont(0.5) WITHIN GROUP (ORDER BY ended_at - started_at) AS median_ride_length    -- Median Ride Length - Note:  No median function in PostGreSQL
FROM
    rides
GROUP BY
    member_casual                                          -- separates by members vs casual
ORDER BY
	DATE_TRUNC('second', AVG(ended_at - started_at)) DESC;  -- Orders by longest Average Ride to least longest Average Ride per membership type
~~~
___

Query for Question #1 (Bike Type Comparisons:  Docked vs Classic vs Electric)
~~~ SQL
-- Average, Max, Mode, & Median ride time by bike type
-- Note: DATE_TRUNC is how you round intervals in PostGreSQL
-- Note:  No median function in PostGreSQL so provided replacement below

SELECT
	rideable_type,
	DATE_TRUNC('second', AVG(ended_at - started_at)) AS avg_ride_length,                          -- Avg Ride Length - Note: DATE_TRUNC is how you round intervals in PostGreSQL
	MIN(ended_at - started_at) AS min_ride_length,                                                -- Min Ride Length
	MAX(ended_at - started_at) AS max_ride_length,                                                -- Max Ride Length
	MODE() WITHIN GROUP (ORDER BY ended_at - started_at) AS mode_ride_length,                     -- Mode Ride Length
	percentile_cont(0.5) WITHIN GROUP (ORDER BY ended_at - started_at) AS median_ride_length      -- Median Ride Length - Note:  No median function in PostGreSQL
FROM
	rides
GROUP BY
	rideable_type                                            -- Separates by bike type
ORDER BY
	DATE_TRUNC('second', AVG(ended_at - started_at)) DESC;    -- Orders by longest Average Ride to least longest Average Ride per bike type
  ~~~
___

Query for Question #2 (Creation of a month_by_month Temp Table)
~~~ SQL
--Number of Casual vs Members & Bike Type choice by month in chronological order.
--Includes total rides
--Includes total casual rides & total member riders
--Includes total bike types used by member types
--To create a VIEW --> CREATE VIEW month_by_month AS
--To add to a VIEW --> CREATE OR REPLACE VIEW month_by_month AS
--To change name of VIEW --> ALTER VIEW month_by_month RENAME TO month
--To remove the VIEW --> DROP VIEW IF EXISTS month_by_month

CREATE OR REPLACE VIEW month_by_month AS      -- Creates month_by_month temp table
SELECT
	date_part('YEAR',started_at) AS year,       -- Year
	date_part('MONTH',started_at) AS month,	    -- Month
	SUM(
		CASE
			WHEN member_casual = 'casual' THEN 1 ELSE 0         -- Casual Count by Month
			END
	) AS num_of_casual,
	SUM(
		CASE
			WHEN rideable_type = 'docked_bike' AND
			member_casual = 'casual' THEN 1 ELSE 0   -- Casual using Docked bikes per month
			END
	) AS num_of_casual_docked,
	SUM(
		CASE
			WHEN rideable_type = 'classic_bike' AND   -- Casual using Classic bikes per month
			member_casual = 'casual' THEN 1 ELSE 0
			END
	) AS num_of_casual_classic,
	SUM(
		CASE
			WHEN rideable_type = 'electric_bike' AND  -- Casual using Electric bikes per month
			member_casual = 'casual' THEN 1 ELSE 0
			END
	) AS num_of_casual_electric,
	SUM(
		CASE
			WHEN member_casual = 'member' THEN 1 ELSE 0         -- Member Count by Month
			END
	) AS num_of_member,
	SUM(
		CASE
			WHEN rideable_type = 'docked_bike' AND
			member_casual = 'member' THEN 1 ELSE 0   -- Member using Docked bikes per month
			END
	) AS num_of_member_docked,
	SUM(
		CASE
			WHEN rideable_type = 'classic_bike' AND
			member_casual = 'member' THEN 1 ELSE 0   -- Member using Classic bikes per month
			END
	) AS num_of_member_classic,
	SUM(
		CASE
			WHEN rideable_type = 'electric_bike' AND  -- Member using Electric bikes per month
			member_casual = 'member' THEN 1 ELSE 0
			END
	) AS num_of_member_electric,
	COUNT(member_casual) AS total_rides
FROM
	rides
GROUP BY
	date_part('YEAR',started_at),     -- Orders by year
	date_part('MONTH',started_at);     -- Orders by month
~~~
___
Tests that the month_by_month temporary table works (Gotta check!)
~~~ SQL
SELECT
	*
FROM
	month_by_month;
~~~
___
Query for Question #2 (Creation of a day_by_day Temp Table)
~~~ SQL
--Number of Casual vs Members by day_of_week
--Includes totals
--To create a VIEW --> CREATE VIEW day_by_day AS
--To add to a VIEW --> CREATE OR REPLACE VIEW day_by_day AS
--To change name of VIEW --> ALTER VIEW day_by_day RENAME TO day
--To remove the VIEW --> DROP VIEW IF EXISTS day_by_day

CREATE OR REPLACE VIEW day_by_day AS
SELECT
	day_of_week,
	SUM(
		CASE
			WHEN member_casual = 'casual' THEN 1 ELSE 0         --Casual Count by Month
			END
	) AS num_of_casual,
	SUM(
		CASE
			WHEN rideable_type = 'docked_bike' AND
			member_casual = 'casual' THEN 1 ELSE 0   --Casual using Docked bikes per month
			END
	) AS num_of_casual_docked,
	SUM(
		CASE
			WHEN rideable_type = 'classic_bike' AND   --Casual using Classic bikes per month
			member_casual = 'casual' THEN 1 ELSE 0
			END
	) AS num_of_casual_classic,
	SUM(
		CASE
			WHEN rideable_type = 'electric_bike' AND  --Casual using Electric bikes per month
			member_casual = 'casual' THEN 1 ELSE 0
			END
	) AS num_of_casual_electric,
	SUM(
		CASE
			WHEN member_casual = 'member' THEN 1 ELSE 0         --Member Count by Month
			END
	) AS num_of_member,
	SUM(
		CASE
			WHEN rideable_type = 'docked_bike' AND
			member_casual = 'member' THEN 1 ELSE 0   --Member using Docked bikes per month
			END
	) AS num_of_member_docked,
	SUM(
		CASE
			WHEN rideable_type = 'classic_bike' AND
			member_casual = 'member' THEN 1 ELSE 0   --Member using Classic bikes per month
			END
	) AS num_of_member_classic,
	SUM(
		CASE
			WHEN rideable_type = 'electric_bike' AND  --Member using Electric bikes per month
			member_casual = 'member' THEN 1 ELSE 0
			END
	) AS num_of_member_electric,
	COUNT(member_casual) AS total_rides
FROM
	rides
GROUP BY
	day_of_week;
~~~
___
Tests that the day_by_day temporary table works (Gotta check!)
~~~ SQL
SELECT
  *
FROM
  day_by_day;
~~~
___
Query for Question #2 (What percent of members vs casual riders per month)
~~~ SQL
-- Percentage of Rides by Month according to membership status
-- References the month_by_month temporary table

SELECT
	year,
	month,
	ROUND(((CAST(num_of_member AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS member_percent,
	ROUND(((CAST(num_of_casual AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS casual_percent
FROM
	month_by_month;
~~~
___
Query for Question #2 (What percent of members vs casual by bike type per month )
~~~ SQL
-- Percentage of Member Type & Bike Type by Month
-- Requires creation of a month_by_month tempoarary table

SELECT
	year,
	month,
	ROUND(((CAST(num_of_casual_docked AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS c_docked,
	ROUND(((CAST(num_of_casual_classic AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS c_classic,
	ROUND(((CAST(num_of_casual_electric AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS c_electric,
	ROUND(((CAST(num_of_member_docked AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS m_docked,
	ROUND(((CAST(num_of_member_classic AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS m_classic,
	ROUND(((CAST(num_of_member_electric AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS m_electric
FROM
	month_by_month;

~~~
___
Question #2:  (What percent of member vs casual per day of week)
~~~ SQL
-- Percentage of Rides by Day of Week according to membership status
-- References the day_by_day temporary table

SELECT
	day_of_week,
	ROUND(((CAST(num_of_member AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS member_percent,
	ROUND(((CAST(num_of_casual AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS casual_percent
FROM
	day_by_day;
~~~
___
Question #2:  (What percent of member vs casual by bike type per day of week)
~~~ SQL
-- Percentage of Rides by Day of Week according to both membership type & bike type
-- Requires creation of a day_by_day tempoarary table

SELECT
	day_of_week,
	ROUND(((CAST(num_of_casual_docked AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS c_docked,
	ROUND(((CAST(num_of_casual_classic AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS c_classic,
	ROUND(((CAST(num_of_casual_electric AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS c_electric,
	ROUND(((CAST(num_of_member_docked AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS m_docked,
	ROUND(((CAST(num_of_member_classic AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS m_classic,
	ROUND(((CAST(num_of_member_electric AS NUMERIC)/CAST(total_rides AS NUMERIC))*100)) AS m_electric
FROM
	day_by_day;
~~~
___
## Deliverable 5:
#### Supporting visualizations and key findings

Revision in Process!  Contact me for the last draft @ jszymc1@gmail.com

___

## Deliverable 6:
#### My top three recommendations based on my analysis

Revision in Process!  Contact me for the last draft @ jszymc1@gmail.com

___

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
