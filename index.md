## Case Study:  How does a Bike-Share navigate Speedy Success?  

***Data Analyst:*** Joe Szymczak-Parker  
***Client/Sponsor:*** Cyclistic Bike-Share  

**Background:**  
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.  

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members.

**Project Purpose:**  
Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Although the pricing flexibility helps Cyclistic attract more customers, Moreno, the Director of Marketing and my manager, believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a very good chance to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.

**Moreno has set a clear goal:**  
Design marketing strategies aimed at converting casual riders into annual members. In order to do that, however, the marketing analyst team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. Moreno and her team are interested in analyzing the Cyclistic historical bike trip data to identify trends

**Scope / Major Project Activities:**


* How do annual members and casual riders use Cyclistic bikes differently?
* Why would casual riders buy Cyclistic annual memberships?
* How to use digital media to influence casual riders to become members?

**Not included:**  
Moreno has assigned me, the junior Data Analyst, to answer the first   question:    

  *How do annual members and casual riders use Cyclistic bikes differently?  Though the other questions are in scope for our company as a whole, they are out of scope for this analysis.*

**Deliverables:**

  * A clear statement of the business task
  * A description of all data sources used
  * Documentation of any cleaning or manipulation of data
  * A summary of my analysis
  * Supporting visualizations and key findings
  * My top three recommendations based on my analysis

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

## Deliverable 1:
#### Clear Statement of Business Task

  * How do annual members and casual riders use Cyclistic bikes differently?

## Deliverable 2:
#### Description of all Data Sources  

For the purposes of this case study, the datasets exhibit a different name because Cyclistic is a fictional company. However, the data itself comes from a real world ride share company and has been made available by Motivate International Inc. under this [License](https://www.divvybikes.com/data-license-agreement/ "Motivate International License").    

The trip data can be found @ [Divvy Tripdata](https://divvy-tripdata.s3.amazonaws.com/index.html "Divvy Tripdata"). I have downloaded it in the form of 12 excel spreadsheets delineated by month starting on 2020-08 and ending with 2021-07.  This spans 1 year of data separated into the following data types:


~~~SQL
CREATE TABLE rides (
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
	)
~~~

## Deliverable 3: Change Log

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

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
