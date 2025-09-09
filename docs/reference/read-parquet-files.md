# Reading Parquet Files

To read a Parquet file, you must use the dedicated `sd.read_parquet()` method. You cannot query a file path directly within the `sd.sql()` `FROM` clause.

The `sd.sql()` function is designed to query tables that have already been registered in the session. When you pass a path like `'s3://...'` to `FROM`, the SQL engine searches for a registered table with that literal name and fails when it's not found, producing a `table not found` error.

## Usage

The correct process is a two-step approach:

1. **Load** the Parquet file into a DataFrame using `sd.read_parquet()`.
1. **Register** the DataFrame as a temporary view using `.createOrReplaceTempView()`.
1. **Query** the view using `sd.sql()`.

```python
# 1. Load the Parquet file from a URL into a DataFrame
df = sd.read_parquet('s3://wherobots-benchmark-prod/SpatialBench_sf=1_format=parquet/building/building.parquet')

# 2. Register the DataFrame as a temporary view named 'buildings'
df.createOrReplaceTempView('buildings')

# 3. Now, query the view using SQL
sd.sql("SELECT * FROM buildings LIMIT 10").show()
```

### Common Errors

Directly using a file path within `sd.sql()` is a common mistake that will result in an error.

**Incorrect Code:**

```python
# This will fail because the SQL engine looks for a table named 's3://...'
sd.sql("SELECT * FROM 's3://wherobots-benchmark-prod/SpatialBench_sf=1_format=parquet/building/building.parquet'")
```

**Resulting Error:**

```
sedonadb._lib.SedonaError: Error during planning: table '...s3://...' not found
```
