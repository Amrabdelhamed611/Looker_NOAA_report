
## Analysis Description
This data analysis address natural disasters effects on US economy and population , so will investigate the NOAA dataset from [National Climatic Data Center](https://www.ncdc.noaa.gov/) of USA to know total effects on both citizens and economics. 


## Data Extraction using BigQuery SQL
shareable link for the [query](https://console.cloud.google.com/bigquery?sq=505007092220:35c34f468590499982f5b7d0612bd4f5)

```sql
Create or replace view `fleet-automata-299312.NOAA_natura_disasters.storms` AS

WITH Geo AS (
    SELECT 
        state_name ,state_fips_code 
    FROM `bigquery-public-data.geo_census_tracts.us_census_tracts_national`) , storms AS (

    SELECT     
        event_type , state_fips_code , injuries_direct , injuries_indirect ,
        deaths_direct ,deaths_indirect , damage_property , damage_crops ,
        CONCAT( ROUND(event_latitude,0), ', ' ,ROUND(event_longitude , 0) ) AS LatLong, 
        LAST_DAY( DATE(event_begin_time)) AS Date ,  
        FROM `bigquery-public-data.noaa_historic_severe_storms.storms_*` 
        WHERE _TABLE_SUFFIX between '2012' and '2022'
          AND injuries_direct is not null 
          AND injuries_indirect is not null
          AND deaths_direct is not null
          AND deaths_indirect is not null
          AND damage_property is not null
          AND damage_crops is not null 
          AND event_latitude is not null 
          AND event_longitude is not null    )

SELECT    
        ST.event_type, G.state_name	, ST.LatLong,  ST.Date ,  
        sum(ST.injuries_direct) AS injuries_direct_sum, 
        sum(ST.injuries_indirect) AS injuries_indirect_sum, 
        sum(ST.deaths_direct) AS deaths_direct_sum, 
        sum(ST.deaths_indirect)AS deaths_indirect_sum, 
        sum(ST.damage_property)AS damage_property_sum, 
        sum(ST.damage_crops) AS damage_crops_sum ,
        count(ST.event_type) AS event_type_count             
FROM storms AS ST 
LEFT JOIN Geo AS G
ON  ST.state_fips_code = G.state_fips_code
GROUP BY 1, 2, 3, 4 ;
```
## Dashboard Data Studio

shareable link for the [dashboard](https://datastudio.google.com/reporting/87d0fd9e-15f1-4969-ad45-9e105f1130d4)

<embed type="https://raw.githubusercontent.com/Amrabdelhamed611/NOAA_report_dashboard/main/dashboard%20image.png" src="pic.jpg">


###End
