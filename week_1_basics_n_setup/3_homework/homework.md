# Question 1. Understanding docker first run
Run docker with the python:3.12.8 image in an interactive mode, use the entrypoint bash.
What's the version of pip in the image?

COMMAND: docker run -it --entrypoint=bash python:3.12.8

root@f169cb620f7e:/# pip --version
pip 24.3.1 from /usr/local/lib/python3.12/site-packages/pip (python 3.12)


# Question 2. Understanding Docker networking and docker-compose
Given the following docker-compose.yaml, what is the hostname and port that pgadmin should use to connect to the postgres database?
hostname: db
port: 5432


# Question 3. Trip Segmentation Count
During the period of October 1st 2019 (inclusive) and November 1st 2019 (exclusive), how many trips, respectively, happened:

## Up to 1 mile - 104838
SELECT COUNT(*)
FROM green_taxi_trips
WHERE trip_distance <=1;

## In between 1 (exclusive) and 3 miles (inclusive), - 199013
SELECT COUNT(*)
FROM green_taxi_trips
WHERE trip_distance > 1 AND trip_distance <= 3;

## In between 3 (exclusive) and 7 miles (inclusive), - 109645
SELECT COUNT(*)
FROM green_taxi_trips
WHERE trip_distance > 3 AND trip_distance <= 7;

## In between 7 (exclusive) and 10 miles (inclusive), - 27688
SELECT COUNT(*)
FROM green_taxi_trips
WHERE trip_distance > 7 AND trip_distance <= 10;

## Over 10 miles - 35202
SELECT COUNT(*)
FROM green_taxi_trips
WHERE trip_distance > 10;


# Question 4. Longest trip for each day
Which was the pick up day with the longest trip distance? Use the pick up time for your calculations.
Tip: For every day, we only care about one single trip with the longest distance.

SELECT DATE(lpep_pickup_datetime),
		MAX(trip_distance)
FROM green_taxi_trips
GROUP BY DATE(lpep_pickup_datetime)
ORDER BY 2 DESC LIMIT 1;

=> 2019-10-31


# Question 5. Three biggest pickup zones
Which were the top pickup locations with over 13,000 in total_amount (across all trips) for 2019-10-18?
Consider only lpep_pickup_datetime when filtering by date.

SELECT gt."PULocationID", 
		z."Zone" as pickup_zone, 
		SUM(gt.total_amount)
FROM green_taxi_trips gt JOIN zones z ON gt."PULocationID" = z."LocationID"
WHERE DATE(gt.lpep_pickup_datetime) = '2019-10-18'
GROUP BY gt."PULocationID", z."Zone"
HAVING SUM(gt.total_amount) > 13000
ORDER BY SUM(gt.total_amount) DESC;

PULocationID    pickup_zone               sum
74              East Harlem North       18686.68000000001
75              East Harlem South       16797.260000000057
166             Morningside Heights     13029.790000000052


# Question 6. Largest tip
For the passengers picked up in October 2019 in the zone name "East Harlem North" which was the drop off zone that had the largest tip?
Note: it's tip , not trip
We need the name of the zone, not the ID.

SELECT dz."Zone", MAX(gt.tip_amount) 
FROM green_taxi_trips gt 
JOIN zones pz ON gt."PULocationID" = pz."LocationID"
JOIN zones dz ON gt."DOLocationID" = dz."LocationID"
WHERE EXTRACT("YEAR" from gt."lpep_pickup_datetime") = 2019
AND EXTRACT("MONTH" from gt."lpep_pickup_datetime") = 10
GROUP BY dz."Zone"
ORDER BY 2 DESC;

Zone                              max
West Chelsea/Hudson Yards       158.52
JFK Airport                     121
...
Yorkville West                  80.88

=> West Chelsea/Hudson Yards is not available in the answer, so I chose JFK Airport


# Question 7. Terraform Workflow
Which of the following sequences, respectively, describes the workflow for:
Downloading the provider plugins and setting up backend,
Generating proposed changes and auto-executing the plan
Remove all resources managed by terraform`

terraform init, terraform apply -auto-approve, terraform destroy
