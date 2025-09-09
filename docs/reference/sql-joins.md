# Spatial Joins

You can perform spatial joins using standard SQL `INNER JOIN` syntax. The join condition is defined in the `ON` clause using a spatial function that specifies the relationship between the geometries of the two tables.

## General Spatial Join

Use functions like `ST_Contains`, `ST_Intersects`, or `ST_Within` to join tables based on their spatial relationship.

### Example

Assign a country to each city by checking which country polygon contains each city point.

```sql
SELECT
    cities.name as city,
    countries.name as country
FROM cities
INNER JOIN countries
ON ST_Contains(countries.geometry, cities.geometry)
```

## K-Nearest Neighbor (KNN) Join

Use the specialized `ST_KNN` function to find the *k* nearest neighbors from one table for each geometry in another. This is useful for proximity analysis.

### Example For each city, find the 5 other closest cities.

```sql
SELECT
    cities_l.name AS city,
    cities_r.name AS nearest_neighbor
FROM cities AS cities_l
INNER JOIN cities AS cities_r
ON ST_KNN(cities_l.geometry, cities_r.geometry, 5, false)
```
