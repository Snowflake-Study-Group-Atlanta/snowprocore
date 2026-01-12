# Snowflake AI Data Cloud Features and Architecture

Snowflake is an overloaded term—it refers to a company, a Software-as-a-Service platform, and the name of a system-defined, read-only database that tracks metadata and usage data about your organization.

The Snowflake data platform is cloud-native, designed specifically for cloud environments. Its three-layer architecture separates compute, storage, and cloud services for independent scaling. The platform works across AWS, Azure, and GCP, enabling customers to leverage existing cloud infrastructure and share data seamlessly across cloud environments.

Snowflake stores data in a compressed, columnar format that accelerates analytical queries by reading only required columns, making it ideal for business intelligence and data warehousing. Its micro-partitioning storage model automatically organizes data for optimal performance.

## Outline key features of the Snowflake Data Cloud

### Interoperable stage

A stage is a location for storing data files for loading into or unloading from Snowflake tables. Stages enable interoperability with external cloud storage. Two main types exist:

- **Internal Stages:** Snowflake-managed storage within your account (user, table, and named stages).
- **External Stages:** Point to your cloud storage (S3, GCS, Azure) for direct data loading and unloading, enabling integration with existing data lakes.

> **Two stage types:** Internal (Snowflake-managed) vs External (your cloud storage: S3, GCS, Azure). External stages enable direct integration with existing data lakes.

### Elastic compute

Elastic compute is delivered through Virtual Warehouses—clusters of compute resources (CPU, memory, temporary storage) that execute queries, data loading, and DML operations. Key capabilities:

- **Scale Up/Down:** Resize warehouses using T-shirt sizes (X-Small to Large) to match workload and control costs.
- **Scale In/Out:** Multi-cluster warehouses automatically add or remove clusters based on concurrent query load.

> **Elastic Compute = Virtual Warehouses**
>
> - Scale Up/Down: Change warehouse size (X-Small → Large)
> - Scale In/Out: Add/remove clusters for concurrency
> - Pay only for compute when using it (separation of storage and compute)

Storage and compute separation enables pay-per-use pricing and independent scaling.

### Snowflake's layers

Snowflake uses a hybrid shared-disk and shared-nothing architecture with three layers:

1. **Database Storage:** Foundation layer storing data in compressed, columnar format within cloud provider storage (AWS, Azure, GCP). Designed for scalability, resilience, and efficiency.
2. **Query Processing (Compute):** Independent virtual warehouses executing queries without shared compute resources, enabling elastic compute.
3. **Cloud Services:** The "brain" coordinating authentication, infrastructure management, metadata management, query optimization, and access control—making Snowflake fully managed.

> **Three-Layer Architecture (Hybrid shared-disk + shared-nothing):**
>
> 1. **Database Storage** - Compressed, columnar format in cloud storage (AWS/Azure/GCP)
> 2. **Query Processing** - Independent virtual warehouses (no shared compute)
> 3. **Cloud Services** - The "brain": authentication, metadata, query optimization, access control

### Overview of Snowflake editions

Snowflake offers four editions with progressively advanced features:

- **Standard:** Introductory edition with core features including architecture, virtual warehouses, and Snowpipe.
- **Enterprise:** Adds multi-cluster warehouses for high concurrency and materialized views.
- **Business Critical:** Adds data protection for sensitive data including HIPAA/PCI compliance and failover/failback.
- **Virtual Private Snowflake (VPS):** Highest level with complete isolation and dedicated environment.

> **Editions progression:** Standard → Enterprise (+ multi-cluster, materialized views) → Business Critical (+ HIPAA/PCI, failover) → VPS (complete isolation)

## Outline key Snowflake tools and interfaces

### Snowsight

Snowsight is Snowflake's modern web interface for:

- Writing and running SQL queries
- Creating and managing database objects (tables, views, warehouses)
- Visualizing results with charts and dashboards
- Monitoring query history and performance
- Managing accounts and users
- Accessing Snowpark, Streamlit, and other features

> **Snowsight** = Modern web UI for SQL, visualizations, monitoring, and management

### SnowSQL

SnowSQL is the command-line client for interactive and batch SQL operations:

- Running ad-hoc queries and SQL scripts
- Loading and unloading data
- Automating database tasks

### Snowflake connectors

Snowflake provides connectors for integrating tools and applications:

- **Spark Connector:** Read/write data in Apache Spark applications
- **Python Connector:** Execute queries and manage objects from Python
- **Kafka Connector:** Stream data from Kafka topics to Snowflake tables

> **Key Connectors:** Spark, Python, Kafka (for streaming data)

### Snowflake drivers

Drivers for various languages enable custom application development:

- **JDBC:** Java applications
- **ODBC:** Open Database Connectivity standard
- **.NET:** .NET framework
- **Go:** Go programming language
- **Node.js:** Server-side JavaScript

### Snowpark

Snowpark is a developer framework supporting Python, Java, and Scala for building data pipelines and applications that run natively in Snowflake. Provides a DataFrame-style API similar to Spark or pandas.

> **Snowpark:** Developer framework for Python/Java/Scala with DataFrame API. Runs natively in Snowflake (pushdown compute).

### SnowCD

SnowCD (Snowflake Connectivity Diagnostic) is a command-line tool for diagnosing network connections, testing latency, DNS resolution, and firewall issues.

### Streamlit in Snowflake

Streamlit enables building and deploying Python web apps natively in Snowflake, creating interactive data applications securely connected to your data without additional infrastructure.

> **Streamlit in Snowflake:** Build and deploy Python web apps natively in Snowflake (no additional infrastructure needed)

### Cortex (AI/ML services)

Snowflake Cortex provides managed AI/ML services requiring no ML expertise:

- **LLM Functions:** Use large language models directly in SQL
- **Forecasting:** Generate time-series forecasts
- **Anomaly Detection:** Identify unusual patterns and outliers
- **Contribution Explorer:** Discover key business metric drivers

> **Cortex AI/ML Services:** LLM Functions (in SQL), Forecasting, Anomaly Detection, Contribution Explorer - no ML expertise required

### Snowflake SQL API

REST API for programmatic SQL execution:

- Submit SQL statements
- Check execution status
- Retrieve results

## Outline Snowflake's catalog and objects

### Databases

Databases are logical groupings of schemas, tables, views, and other objects—the top-level organizational unit for data.

### Stages

Stages are locations for data files, either internal (Snowflake-managed) or external (your cloud storage), used for loading and unloading data.

### Schema types

Schemas logically group database objects within a database. Three types:

- **Permanent:** Standard schemas persisting until explicitly dropped
- **Transient:** No automatic failure recovery, lower storage costs for temporary/staging data
- **Temporary:** Session-based, automatically dropped at session end

> **Three Schema Types:** Permanent (standard), Transient (no failure recovery, lower cost), Temporary (session-based)

### Table types

Five table types with different characteristics:

- **Permanent:** Default type with 7-day Time Travel (90 days Enterprise) and Fail-safe
- **Transient:** Max 24-hour Time Travel, no Fail-safe, for temporary data
- **Temporary:** Session-specific, dropped at session end
- **External:** Query external stage files (S3, Azure, GCS) as tables
- **Iceberg:** Apache Iceberg format enabling table evolution and time travel on data lakes

> **Table Types & Time Travel:**
>
> - **Permanent:** 7-day Time Travel (90 days Enterprise), has Fail-safe
> - **Transient:** 24-hour Time Travel max, NO Fail-safe
> - **Temporary:** Session-specific
> - **External:** Query files in external stages as tables
> - **Iceberg:** Apache Iceberg format with evolution & time travel on data lake

### View types

Views are saved queries that behave like tables. Three types:

- **Standard:** Query executed each time the view is queried
- **Materialized:** Stored results automatically updated when base tables change, improving performance
- **Secure:** Hide query definitions for data privacy, exposing only specific rows/columns

> **Three View Types:**
>
> - **Standard:** Query executed each time
> - **Materialized:** Stored result, auto-updated (performance boost)
> - **Secure:** Hides query definition for data privacy

### Data types

Rich SQL data type support:

- **Numeric:** `NUMBER`, `INTEGER`, `FLOAT`, `DECIMAL`
- **String:** `VARCHAR`, `STRING`, `TEXT`
- **Binary:** `BINARY`, `VARBINARY`
- **Logical:** `BOOLEAN`
- **Date and Time:** `DATE`, `TIME`, `TIMESTAMP`
- **Semi-structured:** `VARIANT`, `ARRAY`, `OBJECT`
- **Geospatial:** `GEOGRAPHY`, `GEOMETRY`

> **Semi-structured data types:** VARIANT, ARRAY, OBJECT (key differentiator for JSON/XML)

### User Defined Functions (UDFs)

UDFs extend Snowflake functionality with custom logic, supporting:

- **SQL:** Simple, scalar functions
- **JavaScript:** Complex logic
- **Java:** Computationally intensive logic (JAR files)
- **Python:** Leverage Python libraries

> **UDFs support:** SQL, JavaScript, Java, Python

### User Defined Table Functions (UDTFs)

UDTFs return row sets, behaving like tables. Written in SQL, JavaScript, Java, or Python for complex data transformations.

### Stored procedures

Stored procedures encapsulate SQL statements and procedural logic for complex business logic and task automation. Written in SQL (Snowflake Scripting), JavaScript, Java, or Python.

### Streams

Streams capture change data capture (CDC) information, recording all inserts, updates, and deletes for processing changed data in pipelines.

> **Streams:** Capture CDC (Change Data Capture) - inserts, updates, deletes. Essential for incremental data pipelines.

### Tasks

Tasks schedule SQL statements or stored procedures for pipeline automation. Can be chained into complex workflows (task trees/DAGs).

> **Tasks:** Scheduled SQL/stored procedures. Can be chained for complex workflows (task trees/DAGs).

### Pipes

Pipes define continuous data ingestion from external stages. Snowpipe automatically loads new files as they arrive—efficient and scalable for streaming data.

> **Pipes (Snowpipe):** Continuous, automatic data ingestion from external stages. Loads files as they arrive.

### Shares

Shares encapsulate database sharing information, enabling Secure Data Sharing between Snowflake accounts and non-Snowflake users via reader accounts.

> **Shares:** Enable Secure Data Sharing between Snowflake accounts (even to non-Snowflake users via reader accounts).

### Sequences

Sequences generate unique number sequences, commonly used for primary key values.

## Outline Snowflake storage concepts

### Micro-partitions

Micro-partitions are Snowflake's fundamental storage unit. Data is automatically divided into immutable micro-partitions (50-500MB uncompressed). Metadata stored per partition (column value ranges) enables efficient query pruning.

> **Micro-partitions:** Fundamental storage unit
>
> - Immutable, 50-500MB uncompressed
> - Automatic partitioning
> - Metadata per partition (column ranges) enables query pruning

### Data clustering

Data clustering co-locates related data in the same or nearby micro-partitions, improving query performance by reducing micro-partition scans. Define a clustering key for automatic maintenance.

> **Data Clustering:** Co-locate related data in same/nearby micro-partitions. Define clustering key → automatic maintenance → improved query performance.

### Data storage monitoring

Monitor storage usage via `ACCOUNT_USAGE` schema views:

- `TABLE_STORAGE_METRICS`: Table storage monitoring
- `STORAGE_USAGE`: Account-level storage overview

Snowsight also provides storage usage visualization for cost understanding and optimization.

> **Storage Monitoring:** Use ACCOUNT_USAGE schema (TABLE_STORAGE_METRICS, STORAGE_USAGE) or Snowsight for visualization.
