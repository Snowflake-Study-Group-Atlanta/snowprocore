# Data Transformations

## Explain how to work with structured data

### Estimation functions

HyperLogLog functions provide fast approximate results on large datasets. `APPROX_COUNT_DISTINCT` is much faster than `COUNT(DISTINCT ...)` with high accuracy.

> **Estimation Functions:** APPROX_COUNT_DISTINCT for fast approximate distinct counts on large data.

### Sampling

Select a random subset from large tables for exploration and analysis without querying entire tables.

### SAMPLE command

Add `SAMPLE` clause to SELECT: `SELECT * FROM my_table SAMPLE (10);`

### /TABLESAMPLE command

Alternative syntax: `SELECT * FROM my_table TABLESAMPLE (10);` Functionally equivalent to `SAMPLE`.

### Sampling methods

#### Fraction-based

`BERNOULLI` or `ROW` sampling. Each row has specified probability of inclusion. More random but scans entire table.

#### Fixed-size

`SYSTEM` or `BLOCK` sampling. Samples entire micro-partitions. Faster (no full scan) but less random if data is clustered.

> **Sampling Methods:** ROW/BERNOULLI (per-row probability, full scan) vs SYSTEM/BLOCK (partition-based, faster).

### Supported function types

#### System functions

Built-in functions for string manipulation, date/time, math operations.

#### Table functions

Return row sets, behave like tables. Examples: `FLATTEN`, `SPLIT_TO_TABLE`.

#### External functions

Call custom code hosted outside Snowflake (AWS Lambda, Azure Functions). Extend functionality with any language.

> **External Functions:** Call AWS Lambda, Azure Functions from SQL. Custom logic in any language.

#### User-Defined Functions (UDFs)

Custom functions stored in Snowflake. Written in SQL, JavaScript, Java, or Python.

> **UDFs:** Custom functions in SQL, JavaScript, Java, or Python.

### Stored procedures

Encapsulate multi-step logic with conditionals and error handling. Written in SQL (Snowflake Scripting), JavaScript, Java, or Python.

### Streams

Capture change data (CDC)—inserts, updates, deletes on a table. Query streams to see only changed rows for incremental processing.

> **Streams:** CDC on tables. Track inserts, updates, deletes. Query for changed rows only.

### Tasks

Schedule SQL or stored procedures. Chain tasks into workflows (task trees/DAGs).

> **Tasks:** Scheduled execution. Chain into DAGs for pipeline automation.

## Explain how to work with semistructured data

### Supported data formats, data types, and sizes

Formats: JSON, Avro, ORC, Parquet, XML. Stored in `VARIANT` type (up to 16MB per value). Also `ARRAY` and `OBJECT` types.

> **Semi-structured Support:** JSON, Avro, ORC, Parquet, XML. VARIANT type (16MB max). Also ARRAY, OBJECT.

### VARIANT column

Stores semi-structured data without predefined schema. Snowflake auto-parses and optimizes storage.

> **VARIANT:** Schema-less column type. Load JSON/XML directly without defining structure.

### Flattening the nested structure

Convert nested data to tabular format.

#### FLATTEN command

Table function producing rows from arrays or key-value pairs from objects.

#### LATERAL FLATTEN command

`LATERAL` allows the flattened subquery to reference columns from the same `FROM` clause. Joins flatten results back to original rows.

> **FLATTEN + LATERAL:** Convert nested arrays/objects to rows. LATERAL joins results to source table.

### Semi-structured data functions

#### ARRAY/OBJECT creation and manipulation

Create: `ARRAY_CONSTRUCT`, `OBJECT_CONSTRUCT`. Manipulate: `ARRAY_APPEND`, `ARRAY_CAT`, `OBJECT_INSERT`.

#### Extracting values

Use colon for object keys: `col:address.city`. Use brackets for arrays: `col:orders[0]`. Also `GET_PATH` function.

> **Value Extraction:** Colon notation (col:key.subkey), brackets (col:array[0]), or GET_PATH function.

#### Type predicates

`IS_` functions check VARIANT value types: `IS_ARRAY`, `IS_OBJECT`, `IS_VARCHAR`, `IS_REAL`, etc.

## Explain how to work with unstructured data

### Define and use directory tables

Enable on a stage to create file metadata manifest. Includes `FILE_URL` column for referencing individual files as rows.

> **Directory Tables:** File metadata on stages. Query files as rows with FILE_URL column.

### SQL file functions

- `GET_PRESIGNED_URL`: Temporary downloadable URL for staged file
- `GET_ABSOLUTE_PATH`: File path for use in UDFs and procedures

### Types of URLs used to access data files

- **Scoped URL:** Temporary, auto-generated for limited-time access
- **File URL:** Permanent path identifying a file in a stage
- **Pre-signed URL:** Shareable download link for external access

> **URL Types:** Scoped (temporary), File (permanent path), Pre-signed (shareable download link).

### Processing unstructured data

Process PDFs, images, audio directly in Snowflake.

#### User-Defined Functions (UDFs) for unstructured data analysis

Java or Python UDFs read and process files. Example: Python with Tika for PDF text extraction, Pillow for image dimensions.

#### Stored procedure

Orchestrate processing: list files from directory table, loop through calling UDFs, store results in structured tables.

> **Unstructured Processing:** Java/Python UDFs process files (PDFs, images). Stored procedures orchestrate batch processing.
