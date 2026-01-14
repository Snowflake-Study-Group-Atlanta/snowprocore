## Define concepts and best practices that should be considered when loading data

### Stages and stage types

Stages are intermediate storage locations for data files:

- **External stage:** Points to your cloud storage (S3, Azure Blob, GCS). Use for most scenarios—integrates with existing data lake infrastructure.
- **Internal stage:** Snowflake-managed storage. Use for ad-hoc loading or without external cloud storage.

> **Stage Types:** External (your cloud storage) vs Internal (Snowflake-managed). External preferred for data lake integration.

### File size and formats

- **File Size:** Optimal is 100-250MB compressed. Multiple files allow parallel loading across warehouse nodes.
- **Formats:** CSV, JSON, Avro, ORC, Parquet, XML. Binary formats (Parquet, Avro, ORC) load faster than text (CSV, JSON).

> **Optimal File Size:** 100-250MB compressed. Enables parallel loading. Binary formats (Parquet, Avro, ORC) faster than text.

### Folder structures

Organize files by logical partitions (e.g., `bucket/app/YYYY/MM/DD/`). Enables loading specific time periods by pointing `COPY` to the relevant path.

### Ad hoc/bulk loading

- **Bulk Loading:** Use `COPY INTO` for large datasets. Optimized for high-volume file loading.
- **Ad Hoc Loading:** Web UI for local files, `INSERT` for small row counts. Not efficient for large loads.

> **Loading Methods:** COPY INTO for bulk (large files), INSERT for small row counts, Web UI for local ad-hoc.

### Snowpipe

Continuous ingestion service. Automatically loads new files from external stages using serverless compute. No warehouse management required.

> **Snowpipe:** Continuous, automatic file ingestion. Serverless compute. Near-real-time loading.

## Outline different commands used to load data and when they should be used

### CREATE STAGE

Defines a stage location and credentials. Required before loading data.

### CREATE FILE FORMAT

Creates a named format specification (file type, compression, delimiter, null handling). Reusable across multiple `COPY` commands.

> **Named File Formats:** Define once, reuse across COPY commands. Specify type, compression, delimiter, null handling.

### CREATE PIPE

Defines a Snowpipe for continuous ingestion. Includes the `COPY INTO` statement and source stage.

### CREATE EXTERNAL TABLE

Query files in external stages without loading. Reads data directly from your data lake.

> **External Tables:** Query stage files directly without loading into Snowflake tables.

### COPY INTO

Primary bulk loading command. Syntax: `COPY INTO <table> FROM @<stage>`. Options include:

- `ON_ERROR`: Error handling behavior
- `MATCH_BY_COLUMN_NAME`: Column mapping
- Transformation expressions during load

> **COPY INTO:** Primary bulk load command. Supports error handling, column mapping, and load-time transformations.

### INSERT/INSERT OVERWRITE

`INSERT` for small row counts. `INSERT OVERWRITE` replaces table contents entirely. Neither efficient for large-scale loading.

### PUT

Uploads local files to internal stages. Required step when loading from local filesystem to internal stage.

### VALIDATE

Tests `COPY INTO` without executing. Reports errors (type mismatches, formatting issues) before actual load.

> **VALIDATE:** Dry-run for COPY INTO. Find errors before loading.

## Define concepts and best practices that should be considered when unloading data

### File size and formats

- `MAX_FILE_SIZE`: Control output file size in `COPY INTO @stage`
- Same format options as loading (CSV, JSON, Parquet, etc.)

#### Overview of compression methods

Supported: GZIP, BZ2, BROTLI, ZSTD. Compression reduces file size, storage cost, and transfer time.

> **Unload Compression:** GZIP, BZ2, BROTLI, ZSTD. Reduces storage and transfer costs.

### Empty strings and NULL values

Control representation with `EMPTY_FIELD_AS_NULL` and `NULL_IF` options in file format or `COPY` command.

### Unloading to a single file

Default: parallel unload to multiple files. Use `SINGLE = TRUE` for one file (slower).

> **Single File Unload:** SINGLE = TRUE option. Slower than parallel multi-file default.

### Unloading relational tables

Separate `COPY INTO @stage` per table. No built-in multi-table export with relationship preservation.

## Outline the different commands used to unload data and when they should be used

### GET

Downloads files from internal stage to local machine. Counterpart to `PUT`.

### LIST

Lists files in a stage (alias: `LS`). Verify unload success and see created file names.

### COPY INTO

Primary unload command. Syntax: `COPY INTO @<stage> FROM <table>`. Same format options as loading.

> **Unload Command:** COPY INTO @stage FROM table. Same format options as loading.

### CREATE STAGE

Required destination for unloaded files.

### CREATE FILE FORMAT

Specifies output file format.
