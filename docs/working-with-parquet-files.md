# Working with Parquet Files

The easiest way to read a GeoParquet or Parquet file is to use `sd.read_parquet()`. Alternatively, you can query these files directly by their path in SQL.

## Install SedonaDB

Use pip to install SedonaDB from the Python Package Index (PyPI).

> **Note**: Before running this notebook on your local machine, you must have SedonaDB installed in your environment. You can install SedonaDB with the following command: `pip install "apache-sedona[db]"`

## Implementation

A common workflow for working with GeoParquet and/or Parquet files is:

1. **Load** the Parquet file into a data frame using `sd.read_parquet()`.
2. **Register** the data frame as a view with `to_view()`.
3. **Query** the view using `sd.sql()`.
4. **Write** your results to a Parquet file with `.to_parquet()` or use `.to_pandas()` to export your results to a DataFrame or GeoDataFrame.


```python
# Import the sedona.db module and connect to SedonaDB
import sedona.db

sd = sedona.db.connect()
```


```python
# 1. Load the Parquet file
df = sd.read_parquet(
    "https://raw.githubusercontent.com/geoarrow/geoarrow-data/v0.2.0/"
    "natural-earth/files/natural-earth_cities_geo.parquet"
)

# 2. Register the data frame as a view
df.to_view("zone")

# 3. Query the view and store the result in a new DataFrame
query_result_df = sd.sql("SELECT * FROM zone LIMIT 10")
query_result_df.show()
```

    ┌──────────────┬───────────────────────────────┐
    │     name     ┆            geometry           │
    │     utf8     ┆            geometry           │
    ╞══════════════╪═══════════════════════════════╡
    │ Vatican City ┆ POINT(12.4533865 41.9032822)  │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ San Marino   ┆ POINT(12.4417702 43.9360958)  │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Vaduz        ┆ POINT(9.5166695 47.1337238)   │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Lobamba      ┆ POINT(31.1999971 -26.4666675) │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Luxembourg   ┆ POINT(6.1300028 49.6116604)   │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Palikir      ┆ POINT(158.1499743 6.9166437)  │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Majuro       ┆ POINT(171.3800002 7.1030043)  │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Funafuti     ┆ POINT(179.2166471 -8.516652)  │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Melekeok     ┆ POINT(134.6265485 7.4873962)  │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Bir Lehlou   ┆ POINT(-9.6525222 26.1191667)  │
    └──────────────┴───────────────────────────────┘



```python
# 4. Write the result to a new Parquet file
output_path = "query_results.parquet"
query_result_df.to_parquet(output_path)

# (Optional) Verify the written file
print(f"\nVerifying the written file at '{output_path}'...")
verified_df = sd.read_parquet(output_path)
verified_df.show(5)
```


    Verifying the written file at 'query_results.parquet'...
    ┌──────────────┬───────────────────────────────┐
    │     name     ┆            geometry           │
    │     utf8     ┆            geometry           │
    ╞══════════════╪═══════════════════════════════╡
    │ Vatican City ┆ POINT(12.4533865 41.9032822)  │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ San Marino   ┆ POINT(12.4417702 43.9360958)  │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Vaduz        ┆ POINT(9.5166695 47.1337238)   │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Lobamba      ┆ POINT(31.1999971 -26.4666675) │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┤
    │ Luxembourg   ┆ POINT(6.1300028 49.6116604)   │
    └──────────────┴───────────────────────────────┘
