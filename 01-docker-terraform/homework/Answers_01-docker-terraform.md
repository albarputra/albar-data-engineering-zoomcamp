# Answer 1. Understanding Docker images
## run docker
docker run -it     --rm     --entrypoint=bash     python:3.13
## check version
pip --version
pip 25.3 from /usr/local/lib/python3.13/site-packages/pip (python 3.13)

## answer
```25.3```

# Answer 2. Understanding Docker networking and docker-compose
refer to 01-docker-terraform/homework.docker-compose.yaml
Host: db (because service name is "db")
port: 5432

## answer
```db:5432```

# Answer 3. Counting short trips
##  table green_tripdata from wget https://d37ci6vzurychx.cloudfront.net/trip-data/green_tripdata_2025-11.parquet

select count(*) from green_tripdata gt 
where 
(gt.trip_distance < 1 or gt.trip_distance = 1)
and
gt.lpep_pickup_datetime between '2025-11-01' and '2025-12-01';

## result
```8007```

# Answer 4. Longest trip for each day
## table green_tripdata from wget https://d37ci6vzurychx.cloudfront.net/trip-data/green_tripdata_2025-11.parquet
SELECT 
    CAST(lpep_pickup_datetime AS DATE) AS pickup_day, 
    MAX(trip_distance) AS max_distance
FROM green_tripdata
WHERE trip_distance < 100
GROUP BY pickup_day
ORDER BY max_distance DESC
LIMIT 1;

## result
```pickup_day = 2025-11-14```
max_distance = 88.03



# Answer 5. Biggest pickup zone
## table green_tripdata from wget https://d37ci6vzurychx.cloudfront.net/trip-data/green_tripdata_2025-11.parquet

## table taxi_zone_lookup from wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv

SELECT 
    z."Zone", 
    SUM(g.total_amount) AS total_amount_sum
FROM public.green_tripdata g
JOIN public.taxi_zone_lookup z 
    ON g."PULocationID" = z."LocationID"
WHERE CAST(g.lpep_pickup_datetime AS DATE) = '2025-11-18'
GROUP BY z."Zone"
ORDER BY total_amount_sum DESC
LIMIT 1;

## result
```Zone = 25.3```
total_amount_sum = 9281.919999999991

# Answer 6. Largest tip
## table green_tripdata from wget https://d37ci6vzurychx.cloudfront.net/trip-data/green_tripdata_2025-11.parquet

## table taxi_zone_lookup from wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv

SELECT 
    z_do."Zone" AS dropoff_zone, 
    MAX(g.tip_amount) AS largest_tip
FROM public.green_tripdata g
JOIN public.taxi_zone_lookup z_pu 
    ON g."PULocationID" = z_pu."LocationID"
JOIN public.taxi_zone_lookup z_do 
    ON g."DOLocationID" = z_do."LocationID"
WHERE 
    z_pu."Zone" = 'East Harlem North' 
    AND g.lpep_pickup_datetime >= '2025-11-01 00:00:00' 
    AND g.lpep_pickup_datetime < '2025-12-01 00:00:00'
GROUP BY z_do."Zone"
ORDER BY largest_tip DESC
LIMIT 1;

## result
```dropoff_zone = Yorkville West ```
Yorkville West = 81.89

# Answer 7. Terraform Workflow
Downloading plugins and setting up backend: The command is terraform init. This is the first command that must be run after writing a new Terraform configuration. It initializes the working directory, downloads the necessary provider plugins (like AWS, Google Cloud, or Postgres), and configures the backend for storing the state file.

Generating proposed changes and auto-executing the plan: The command is terraform apply -auto-approve. While terraform plan only shows changes, terraform apply actually generates the plan and executes it. The flag -auto-approve is specifically used to skip the interactive manual confirmation, allowing it to "auto-execute" the proposed changes.

Removing all resources: The command is terraform destroy. This command looks at your state file and deletes every resource that Terraform is currently managing.

## answer
```terraform init, terraform apply -auto-approve, terraform destroy```