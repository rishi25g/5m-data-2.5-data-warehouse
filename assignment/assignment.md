# Assignment

## Brief

Write the SQL statements for the following questions.

## Instructions

Paste the answer as SQL in the answer code section below each question.

### Question 1

Let's revisit our `austin_bikeshare_demo` dbt project. Modify the `dim_station.sql` model to include the following columns:

- `total_duration` (sum of `duration` for each station in seconds)
- `total_starts` (count of `start_station_name` for each station)
- `total_ends` (count of `end_station_name` for each station)

Then, rebuild the models with the following command to see if the changes are correct:

```bash
dbt run
```

Answer:

Paste the `dim_station.sql` model here:

```
WITH stations AS (

    SELECT DISTINCT 
        station_id,
        name,
        status,
        location,
        address,
        alternate_name,
        city_asset_number,
        property_type,
        number_of_docks,
        power_type,
        footprint_length,
        footprint_width,
        notes,
        council_district,
        image,
        modified_date
    FROM {{ source('austin_bikeshare', 'bikeshare_stations') }}

),

trip_starts AS (

    SELECT
        start_station_name AS station_name,
        COUNT(start_station_name) AS total_starts,
        SUM(duration * 60) AS total_duration  -- convert minutes to seconds
    FROM {{ source('austin_bikeshare', 'bikeshare_trips') }}
    GROUP BY start_station_name

),

trip_ends AS (

    SELECT
        end_station_name AS station_name,
        COUNT(end_station_name) AS total_ends
    FROM {{ source('austin_bikeshare', 'bikeshare_trips') }}
    GROUP BY end_station_name

)

SELECT
    s.station_id,
    s.name,
    s.status,
    s.location,
    s.address,
    s.alternate_name,
    s.city_asset_number,
    s.property_type,
    s.number_of_docks,
    s.power_type,
    s.footprint_length,
    s.footprint_width,
    s.notes,
    s.council_district,
    s.image,
    s.modified_date,
    COALESCE(ts.total_duration, 0) AS total_duration,
    COALESCE(ts.total_starts, 0) AS total_starts,
    COALESCE(te.total_ends, 0) AS total_ends

FROM stations s
LEFT JOIN trip_starts ts
    ON s.name = ts.station_name
LEFT JOIN trip_ends te
    ON s.name = te.station_name

```

## Submission

- Submit the URL of the GitHub Repository that contains your work to NTU black board.
- Should you reference the work of your classmate(s) or online resources, give them credit by adding either the name of your classmate or URL.
