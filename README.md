# Group 6-MIST-4610 Group Project 2

## Team Name: 
71552 Group 6 

## Team Members:
1. Jesse Williams: [@jesnw](https://github.com/jeswn)
2. Tania Saputera: [@tas45087](https://github.com/tas45087)
3. Kate Nelms: [@ksn56497](https://github.com/ksn56497)
4. John Omolon: [@JohnOmolon](https://github.com/JohnOmolon)
5. Johan Jerry: [@johanjerry](https://github.com/johanjerry)


## Dataset Description

### Our Dataset and Why:
The dataset our team selected is from the U.S. Department of Transportation, which provides information on non-stop domestic flight segments such as departures and scheduled flights. Our team selected this dataset because it allows us identify major travel hubs in the USA by analyzing flight patterns, and evaluate differences between scheduled and actual flight activity. With this data we can make an meaningful impact on USA airports operationally, economicly, and socially by understanding travel demand and efficiency across regions in the USA.

### Description:
The dataset is provided by the U.S. Department of Transportation and is available through the Snowflake Data Marketplace under the listing US Department of Transportation Data. It contains information on U.S. domestic flight operations.

The dataset consists of seven main tables: Aircraft Carrier Index, Aircraft Index, Airport Index, USDOT Attributes, USDOT Attributes Point-in-Time History, USDOT Timeseries, and USDOT Timeseries Point-in-Time History. The Timeseries tables contain millions of records, capturing flight activity over time.

Key columns include DATE (date, data type: DATE), ORIGIN_AIRPORT_ID (airport identifier, data type: INTEGER), VARIABLE (metric type such as departures scheduled or performed, data type: STRING), and VALUE (numeric measurement, data type: NUMBER). 

### Two Analytical Questions:
How do seasonal changes affect flight reliability in the U.S., based on the difference between scheduled departures, actual departures, cancellations, and extra flights?
This question uses columns such as DATE, VARIABLE, and VALUE, along with ORIGIN_AIRPORT_ID for joining purposes. The DATE column uses a datetime function to group data into seasons, while VARIABLE and VALUE are used to calculate total scheduled and actual departures. This query is non-trivial because it applies datetime transformations, conditional aggregation with CASE WHEN statements, and adds measures such as cancellations and extra flights within a single query. It is meaningful operationally because it helps airlines understand seasonal disruptions to improve scheduling and traffic, economically because it shows periods of inefficiency and potential revenue loss, and socially because it provides insight into the reliability of air travel across different times of the year. 

Which airports exceed the average number of departing flights and serve as primary travel hubs in the U.S.?

This question uses columns such as ORIGIN_AIRPORT_ID, AIRPORT_NAME, VARIABLE, and VALUE. The VARIABLE column is used to filter for departures, while VALUE is aggregated to calculate total flights per airport, and the join with the airport table provides readable airport names. This query is non-trivial because it combines aggregation, grouping, a subquery to calculate the average number of departures, and a HAVING clause to filter results above that average. It is meaningful operationally because it identifies major hubs where airlines allocate resources efficiently, economically because it reveals where travel demand and revenue are highest, and socially because it shows how air travel connectivity is distributed across regions in the United States. 


## Questions and Justification
**Query 1:**

Question:
How do seasonal changes affect flight reliability in the U.S., based on the difference between scheduled departures, actual departures, cancellations, and extra flights? 

Justification:
This query analyzes how flight activity differs across the seasons (Winter, Spring, Summer, Fall) by comparing the total scheduled flights to actual flights flown, while calculating cancellations and extra flights. Operationally, it is meaningful because it helps airlines and airports understand how patterns change with the seasons to improve scheduling accuracy and better prepare for disruptions such as weather delays and holidays. Economically, it identifies periods of high and low efficiency, showing when airlines may lose revenue due to cancellations or gain revenue from increased demand during peak seasons. Socially, our query displays how reliable air travel is throughout the year (especially during busy travel seasons like holidays) and potential impacts for a passengers ability to travel. The query uses the US_DEPARTMENT_OF_TRANSPORTATION_TIMESERIES table (including DATE, VARIABLE, VALUE, and ORIGIN_AIRPORT_ID) and the AIRPORT_INDEX table (AIRPORT_ID). 
<img width="502" height="294" alt="Screenshot 2026-04-27 at 7 52 54 PM" src="https://github.com/user-attachments/assets/a4cb430d-aac1-452a-bbf3-bf4d80aab30c" />

**Query 2:**

Question: 
Which airports exceed the average number of departing flights and serve as primary travel hubs in the U.S.?  

Justification:
This query identifies the airports with the highest number of departing flights by adding the total number of departures and filtering for those above the overall average using a subquery and “having” clause. This query is meaningful because it locates the major travel hubs around the USA, helping airports and the airlines that fly with them make informed operational decisions and better manage air traffic and resources. Economically, it identifies where travel demand and revenue are strongest vs weakest, making certain airports more competitive than others for investment. Socially, our query shows how accessible air travel is across different regions of the USA, locating which areas are more connected than others. The query uses the US_DEPARTMENT_OF_TRANSPORTATION_TIMESERIES table (specifically VALUE, VARIABLE, and ORIGIN_AIRPORT_ID) and the AIRPORT_INDEX table (AIRPORT_ID, AIRPORT_NAME). 
<img width="499" height="152" alt="Screenshot 2026-04-27 at 7 54 29 PM" src="https://github.com/user-attachments/assets/29944779-616a-4c19-8457-1242ed89fae7" />


## Data Manipulations

**Question 1:**
How do seasonal changes affect flight reliability in the U.S., based on the difference between scheduled departures, actual departures, cancellations, and extra flights? 
To answer this question, we joined the US Department of Transportation Time Series table to the Airport Index Table to associate each flight record with a specific location. We also filtered the data to include only data involving the U.S, using COUNTRY_GEO_ID = ‘country/USA.’ To organise the data and make it easier to read, we used the DATE column and a GROUP BY to group records by season (Winter, Spring, Summer, Fall)  based on the month. We also used conditional aggregations (CASE WHEN) to calculate the scheduled and actual number of flights. We then used more case statements to figure out cancellations and extra flights by comparing the totals for scheduled vs performed departures. If there were more scheduled flights than performed flights, we calculated the difference as cancellations and vice versa with extra flights. 



**Question 2:**
Which airports exceed the average number of departing flights and serve as primary travel hubs in the U.S.?
For this question, we used a CASE statement that assigns each airport to a region based on its STATE_GEO_ID. We then joined the Time Series table with the Airport Index table using AIRPORT_ORIGIN_ID = AIRPORT_ID to match each flight record with its airport name and location. We filtered it using VARIABLE = ‘DEPARTURES PERFORMED’ to include only the completed departed flights. To calculate total departures, we used SUM(usdot_ts.VALUE to, and implemented GROUP BY (region, AIRPORT_NAME) to group the data at the airport level within each region. We also used a HAVING clause to compare each airport’s total flights to the average total departures across all airports. This is calculated through a subquery summing all departures per airport and then averaging those totals (AVG()). Only airports with total_flights greater than the calculated average are recorded, which filters out lower-traffic airports. We sorted the resulting airports with ORDER BY total_flights DESC, region and LIMIT 10;. This gives us the 10 busiest airports that operate above the overall average along with the region they belong to.


## Analysis and Results
<img width="500" height="207" alt="Screenshot 2026-04-27 at 7 55 23 PM" src="https://github.com/user-attachments/assets/999a9787-b763-4a54-84c8-6209f6e10a60" />

**Analysis of Question 1:**
The first question gave insight into the flight performance during different seasons. The chart shows that summer is the peak season for flights, and the US maintains a similar number of flights throughout the year. This is likely because the US economy does not rely on tourism, which would show increased seasonality. The chart also shows that while summer, spring, and fall do not have any cancelled flights, winter has roughly 153K. There are also a considerable number of extra flights during summer, spring, and fall, which suggests that carriers need to add additional flights to their offerings throughout these seasons. All these insights give different airline carriers information on when to supply more flights, and when are good times to increase or decrease flight prices. 


Analysis of Question 2:
This chart illustrates how flight concentrations are generally dominated by a small concentration of airports, with Hartsfield Jackson, Chicago O’Hare and Dallas/ Fort Worth International being the most dominant. Past the top 3 most concentrated airports, there is a noticeable drop off in flights, and we notice a second group, Denver and Los Angeles International, also having a high volume of flights, but not on the scale of the first three. Then, the rest of the airports level out, averaging around 7 million total flights. We can see a trend where airports located in the South tend to have the highest ceiling for flight activity than the others, as noted by Hartsfield-Jackson and Dallas-Fort Worth. The West comes at a close second, with a relatively consistent and strong stream of flights coming from Denver, Los Angeles, and Charlotte Douglas International. Finally we can see the Midwest region with more of a mixed concentration, containing both one of the busiest airports in the group (Chicago O’ Hare) and two of the more lower volume airports (Detroit Metro and Minneapolis- St). This helps us visualize how air traffic in the U.S is largely centralized, with few key airports acting as primary hubs and others operating at more steady, consistent levels.
****

## Streamlit App

https://app.snowflake.com/streamlit/sfedu02/nvb91767/#/apps/z7gkegvrrk37c2cemnqq 

<img width="500" height="263" alt="Screenshot 2026-04-27 at 7 56 58 PM" src="https://github.com/user-attachments/assets/bc9ca40e-d8cc-46f2-a217-c3ad26c2b20f" />
<img width="498" height="252" alt="Screenshot 2026-04-27 at 7 57 18 PM" src="https://github.com/user-attachments/assets/6854d5b9-074b-4ee6-ab9a-2aebed3f29d4" />
<img width="498" height="267" alt="Screenshot 2026-04-27 at 7 57 33 PM" src="https://github.com/user-attachments/assets/655aaabb-dd27-45d2-a73c-e7415b273ff6" />
<img width="498" height="265" alt="Screenshot 2026-04-27 at 7 57 45 PM" src="https://github.com/user-attachments/assets/6a5e2b94-7d58-4073-8097-3dd6e5cb30a8" />

### Chart Descriptions
 Seasonal Analysis
Scheduled vs. Actual Season
The bar chart compares the number of scheduled departures to the number of performed departures for each season. Through the results of this chart, we are able to conclude that Summer is the busiest season and that there is consistently a slightly higher number of scheduled departures compared to performed departures.
Cancellation Rate by Season
This bar chart allows us to display the percentages of scheduled flights per season that were cancelled. This data demonstrates that there were more cancellations during the Winter season compared to other seasons
Completion rate detail
This chart delivers us information on the percentage of flights that were completed by each season. Through this data, we are able to conclude that Winter was the only season that did not achieve over 100% completion

Airport Rankings
Top Airports by total departures performed
This chart shows the busiest US airports by the total number of departures performed, with different colors that also indicate the airports by region. This depicts that Hartsfield-Jackson Atlanta (ATL) has the largest number of departures, while Minneapolis-St Paul (MN) has the lowest. 
Region Breakdown
This chart breaks down the number of departures solely by region. The South has the highest number of departures, at 118.7M, with the Northeast having the least, at 44.2M

Monthly Trends
Monthly flight volume & cancellations
This chart allows us to see total monthly flights along with the total number of cancellations. Through this, we are able to see a spike in cancellations in February, and the relatively low amount of cancellations starting in March.
Monthly cancellation rate (%)
This chart supports the Monthly Flight Volume & Cancellations chart by depicting that January and February were really the only months in which there were percentages of cancellations. With this, the chart shows that the spike of cancellations in February was still relatively low, as they were still below 2% of all flights. With this we can also see the best month to cancel (March), the worst month to cancel (February), the peak volume of flights (August), and the slowest volume of flights (January).

Year-over-Year
Year-over-year flight trends
Annual Flights performed
This line graph allows us to see the growth in the number of flights from 1990 to 2024. Along with this, we are able to see trends, such as the large spike downwards in flights in 2020.
Annual Cancellation Rate
This bar chart portrays percentages of flights that were cancelled each year from 1990 to 2024, with red being above average. From this data, we can see a large spike in cancellations in 2003, with the percentage being above 5%.


Claude was prompted to use the existing Streamlit app and implement interactive elements, organise information into separate tabs, and also make the dashboard more visually pleasing. Prompts included specifications to confirm the code would be snowflake compatible, give real business insights, and organise data effectively. 

