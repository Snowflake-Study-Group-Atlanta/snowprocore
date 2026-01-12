# Data Protection and Data Sharing

## Outline Continuous Data Protection with Snowflake

### Time Travel

Access historical data within the retention period. Use `AT` or `BEFORE` clauses to query past states. Recover from accidental drops or incorrect updates.

- Standard: 1 day retention
- Enterprise+: Up to 90 days

> **Time Travel:** Query historical data with AT/BEFORE clauses. 1 day (Standard) to 90 days (Enterprise+).

### Fail-safe

7-day recovery period after Time Travel expires. Data recoverable only by Snowflake support for disaster scenarios. Not for routine recovery—not user-queryable.

> **Fail-safe:** 7 days after Time Travel ends. Snowflake support recovery only. Last resort for disasters.

### Data encryption

Always-on encryption at rest and in transit:

- **At Rest:** AES-256 encryption. Snowflake manages keys hierarchically.
- **In Transit:** TLS for all communication.
- **Tri-Secret Secure:** Customer-managed master key for full encryption control.

> **Encryption:** AES-256 at rest, TLS in transit. Tri-Secret Secure for customer-managed keys.

### Cloning

Zero-Copy Cloning creates instant copies of databases, schemas, or tables without duplicating storage. Clone and original share unchanged micro-partitions. Changes create new partitions.

> **Zero-Copy Cloning:** Instant copy without storage duplication. Shared partitions until modified. Use for dev/test environments.

### Replication and failover

Replicate databases to secondary accounts in different regions or cloud providers. Failover to secondary during outages for business continuity.

> **Replication:** Copy databases to other regions/clouds. Failover for disaster recovery and business continuity.

## Outline Snowflake data sharing capabilities

### Account types

- **Provider Account:** Standard account sharing data with others.
- **Consumer Account:** Receives shared data. Can be full Snowflake customer or a reader account (provider-created for non-Snowflake users).

> **Sharing Accounts:** Providers share data. Consumers receive it. Reader accounts for non-Snowflake users.

### Snowflake Marketplace

Public marketplace for discovering and accessing third-party datasets. Consumers browse and access data. Providers publish datasets.

### Data Exchange

Private sharing hub. Create branded environments for customers, partners, and suppliers to share data.

> **Marketplace vs Exchange:** Marketplace = public. Data Exchange = private, branded hub.

### Access control options

#### DDL commands to create and manage shares

- `CREATE SHARE`: Create share object
- `GRANT USAGE ON DATABASE <db> TO SHARE <share>`: Grant database access
- `GRANT SELECT ON TABLE <table> TO SHARE <share>`: Grant table access
- `ALTER SHARE <share> ADD ACCOUNTS = <account>`: Add consumer

#### Privileges required for working with shares

`CREATE SHARE` privilege for account. `USAGE` on database and `SELECT` on tables being shared.

> **Share Privileges:** CREATE SHARE at account level. USAGE + SELECT on shared objects.

### Secure Data Sharing

Share live, read-only data without copying or moving. Data stays current. No storage duplication.

> **Secure Data Sharing:** Live, read-only access. No data copy. Always current.

#### Direct shares

One-to-one sharing with specific consumer accounts. Common for known partners or internal departments.

#### Data listings

Shares offered on Marketplace or Data Exchange. Public or private. Can be free or paid. Includes metadata, sample queries, terms.

> **Direct Shares vs Listings:** Direct = specific accounts. Listings = discoverable on Marketplace/Exchange.
