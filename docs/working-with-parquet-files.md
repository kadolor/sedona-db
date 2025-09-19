
<!---
  Licensed to the Apache Software Foundation (ASF) under one
  or more contributor license agreements.  See the NOTICE file
  distributed with this work for additional information
  regarding copyright ownership.  The ASF licenses this file
  to you under the Apache License, Version 2.0 (the
  "License"); you may not use this file except in compliance
  with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing,
  software distributed under the License is distributed on an
  "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  KIND, either express or implied.  See the License for the
  specific language governing permissions and limitations
  under the License.
-->

# Working with Parquet Files

To read a GeoPaquet or Parquet file, you must use the dedicated `sd.read_parquet()` method. You cannot query a file path directly within the `sd.sql()` `FROM` clause.

The `sd.sql()` function is designed to query tables that have already been registered in the session.

## Install SedonaDB

Use pip to install SedonaDB from the Python Package Index (PyPI).

```bash
pip install "apache-sedona[db]"
```

## Implementation

To read a geoparquet or parquet file with SedonaDB, you must:

1. **Load** the Parquet file into a data frame using `sd.read_parquet()`.
1. **Register** the data frame as a view with `to_view()`.
1. **Query** the view using `sd.sql()`.
1. **Write** to a Parquet file with `sd.to_parquet()`.

```python linenums="1" title="Read and write parquet files with SedonaDB"

import sedona.db
sd = sedona.db.connect()

# 1. Load the Parquet file
df = sd.read_parquet(
    'https://raw.githubusercontent.com/geoarrow/geoarrow-data/v0.2.0/'
    'natural-earth/files/natural-earth_cities_geo.parquet'
)

# 2. Register the data frame as a view
df.to_view("zone")

# 3. Query the view and store the result in a new DataFrame
query_result_df = sd.sql("SELECT * FROM zone LIMIT 10")
query_result_df.show()

# 4. Write the result to a new Parquet file
output_path = "query_results.parquet"
query_result_df.to_parquet(output_path)

# (Optional) Verify the written file
print(f"\nVerifying the written file at '{output_path}'...")
verified_df = sd.read_parquet(output_path)
verified_df.show(5)
```

### Common Errors

Directly using a file path within `sd.sql()` is a common mistake that will result in an error.

**Incorrect Code:**

```python
# This will fail because the SQL engine looks for a table named 's3://...'
sd.sql("SELECT * FROM 's3://FILE-PATH.parquet'")
```

**Resulting Error:**

When you pass a path like `'s3://...'` to `FROM`, the SQL engine searches for a registered table with that literal name and fails when it's not found, producing a `table not found` error.

``` { .sh .no-copy }
sedonadb._lib.SedonaError: Error during planning: table '...s3://...' not found
```
