# Performance and Cost Optimization Concepts

## Explain the use of the Query Profile

The Query Profile in Snowsight shows how Snowflake executed a query. Access it from Query History to analyze performance.

### Explain plans

The query profile displays the execution plan as a graphical tree of operators (table scans, joins, aggregations). Each node shows execution time, helping identify bottlenecks.

> **Query Profile:** Visual execution plan. Nodes show operators and timing—use to find bottlenecks.

### Data spilling

Spilling occurs when a warehouse lacks memory for intermediate results. Data spills first to local SSD, then to remote storage. Disk I/O is slower than memory, so spilling degrades performance.

> **Data Spilling:** Memory overflow to disk. Local SSD first, then remote storage. Indicates warehouse may be too small.

### Use of the data cache

The profile shows cache efficiency—percentage of data read from the warehouse's SSD cache versus remote storage. Higher cache hit rates mean better performance.

### Micro-partition pruning

The profile shows partition pruning effectiveness: total partitions versus partitions scanned. High scan ratios suggest adding a clustering key.

> **Partition Pruning:** Profile shows total vs scanned partitions. Poor pruning = consider clustering key.

### Query history

Query History in Snowsight lists all executed queries. Filter and search to find specific queries, then open their Query Profile.

## Explain virtual warehouse configurations

### Types of warehouses

- **Standard Warehouse:** Default type for general query workloads.
- **Snowpark-optimized Warehouse:** Larger memory for Snowpark workloads with memory-intensive operations.

> **Warehouse Types:** Standard (general purpose) vs Snowpark-optimized (larger memory for Snowpark).

### Multi-clustering warehouses

Multi-cluster warehouses (Enterprise+) automatically scale compute for high concurrency.

#### Scaling policies

- **Standard:** Starts new clusters quickly to handle load. Default policy, best for unpredictable workloads.
- **Economy:** Maximizes existing cluster utilization before starting new ones. Saves credits but may queue queries.

> **Scaling Policies:** Standard (fast scale-out, may over-provision) vs Economy (maximize utilization, may queue).

#### Scaling modes

- **Maximized:** Starts all clusters up to the maximum immediately.
- **Auto-scale:** Default. Starts/stops clusters based on query load.

### Warehouse sizing

Sizes range from X-Small to 6X-Large. Larger = more CPU and memory. Start small and scale up if you see data spilling or slow complex queries.

> **Sizing:** Start small, scale up if spilling occurs. Each size up doubles compute and cost.

### Warehouse settings and access

- `AUTO_SUSPEND`: Suspends warehouse after inactivity period (seconds). Critical for cost control.
- `AUTO_RESUME`: Automatically resumes when a query arrives.
- Access controlled by `USAGE` privilege granted to roles.

> **Key Settings:** AUTO_SUSPEND (cost control), AUTO_RESUME (convenience). Grant USAGE to roles for access.

## Outline virtual warehouse performance tools

### Monitoring warehouse loads

Snowsight dashboards show running queries, queued queries, and utilization. Use to determine if warehouse is properly sized.

### Scaling up compared to scaling out

- **Scale Up:** Increase warehouse size. Use for complex, long-running queries needing more memory/CPU.
- **Scale Out:** Add clusters (multi-cluster). Use for high concurrent query volume.

> **Scale Up vs Out:** Up = bigger warehouse for complex queries. Out = more clusters for concurrency.

### Query acceleration service

QAS offloads portions of large ad-hoc queries to serverless compute. Boosts performance for occasional large queries without running a larger warehouse full-time.

> **Query Acceleration Service:** Serverless compute boost for large ad-hoc queries. Cost-effective alternative to larger warehouse.

## Optimize query performance

### Describe the use of materialized views

Materialized views store pre-computed query results. Queries against the view read stored results instead of re-executing. Snowflake automatically maintains the view as base data changes.

> **Materialized Views:** Pre-computed, stored results. Auto-maintained. Faster reads, but has storage and maintenance costs.

### Use of specific SELECT commands

Select only needed columns instead of `SELECT *`. Reduces data read from storage and network transfer.

### Clustering

Clustering keys co-locate related data in micro-partitions. Improves pruning for queries filtering on clustered columns.

> **Clustering:** Define clustering key on large tables. Improves pruning for filtered queries.

### Search optimization service

Builds a search access path for fast point-lookup queries (returning few rows). Useful for dashboards and interactive apps on large tables.

> **Search Optimization:** For point-lookups on large tables. Fast single-row or few-row queries.

### Persisted query results

See Result cache below.

### Understanding the impact of different types of caching

Three cache layers:

#### Metadata cache

Cloud Services layer caches table metadata (partition count, column value ranges). Enables fast partition pruning without scanning.

#### Result cache

Stores query results for 24 hours. Identical queries with unchanged data return cached results instantly—no compute cost.

> **Result Cache:** 24-hour cache of query results. Same query + unchanged data = instant, free results.

#### Warehouse cache

SSD cache on warehouse nodes stores recently accessed table data. Faster than remote storage reads.

> **Three Caches:** Metadata (pruning info), Result (24hr query results), Warehouse (SSD table data).

## Describe cost optimization concepts and best practices in Snowflake

### Understanding and exploring the costs of different Snowflake features and services

#### Cost insights feature in Snowsight

Built-in dashboards show costs by compute, storage, and serverless features. Drill down to individual warehouses and services.

#### Use of different table types and sizes

Use `TRANSIENT` tables for temporary/staging data—no Fail-safe period, lower storage cost.

#### Use of views

Standard views have no storage cost. Materialized views incur storage and compute costs for maintenance.

#### Use of search optimization paths

Search Optimization has storage cost (search access path) and compute cost (building/maintaining).

#### Storage costs

Charged per terabyte per month.

#### Compute costs

Charged in credits per second with 60-second minimum.

> **Cost Components:** Storage (TB/month), Compute (credits/second, 60s min), Serverless (feature-specific).

### Understand and explore cloud services costs in Snowflake

Cloud Services usage is free up to 10% of daily compute credits. Overage is billed.

> **Cloud Services:** Free up to 10% of daily compute. Overage billed.

### Costs considerations when using serverless features

Snowpipe, Automatic Clustering, Search Optimization run on Snowflake-managed serverless compute with separate pricing.

### Cost considerations when moving data among regions

#### Replication

Storage cost in secondary account plus data transfer cost between regions.

#### Fail-over

Compute costs in secondary account when it becomes primary.

### Monitor and control costs

#### Resource monitors

Track credit usage for warehouses. Configure alerts or auto-suspend at thresholds.

> **Resource Monitors:** Track warehouse credits. Set alerts and auto-suspend thresholds.

#### Snowflake Budgets service

Set monthly spending budgets and track actual spend against them.

### Attribute costs

#### Cost center tagging

Tag warehouses and objects with department names. Use tags in `ACCOUNT_USAGE` views for cost attribution.

#### Use of the ACCOUNT USAGE schema

Query views like `WAREHOUSE_METERING_HISTORY`, `STORAGE_USAGE`, `SERVERLESS_TASK_HISTORY` for detailed cost analysis.

> **Cost Attribution:** Tag objects, query ACCOUNT_USAGE views (WAREHOUSE_METERING_HISTORY, STORAGE_USAGE) for analysis.
