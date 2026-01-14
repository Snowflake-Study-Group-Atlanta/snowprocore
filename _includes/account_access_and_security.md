# Account Access and Security

## Outline security principles

### Network security and policies

Network policies control access to Snowflake based on IP addresses. Administrators define allowed or blocked IP addresses to restrict connections to trusted locations (corporate networks, VPNs).

> **Network Policies:** Restrict account access by IP address. Whitelist trusted locations, block unknown IPs.

### Multi-Factor Authentication (MFA) enforcement

MFA requires a second authentication factor beyond passwords. Snowflake uses Duo Security for MFA, which can be enabled per-user or enforced account-wide. Users authenticate via an authenticator app on their smartphone.

> **MFA:** Uses Duo Security. Can be enabled per-user or enforced account-wide. Protects against compromised passwords.

### Federated authentication

Federated authentication uses an external SAML 2.0-compliant identity provider (IdP) to authenticate users. Users log in with their existing corporate credentials (Okta, Azure AD, Ping Identity) instead of separate Snowflake passwords.

> **Federated Auth:** SAML 2.0 IdP integration. Users authenticate with corporate credentials (Okta, Azure AD, Ping Identity).

### Key pair authentication

Key pair authentication uses public-private key cryptography for programmatic access. The public key is assigned to a Snowflake user; the private key signs a JWT for authentication. The private key never leaves the client machine.

> **Key Pair Auth:** For programmatic access (drivers, connectors). More secure than passwords—private key never transmitted.

### Single Sign-On (SSO)

SSO allows users to authenticate once with their IdP and access multiple applications including Snowflake. Enabled through federated authentication—users are redirected to the IdP login page and automatically logged into Snowflake upon successful authentication.

## Define the entities and roles that are used in Snowflake

### Overview of access control

Snowflake combines two access control frameworks:

- **Discretionary Access Control (DAC):** Object owners grant access to their objects.
- **Role-Based Access Control (RBAC):** Privileges are assigned to roles, roles are granted to users. A user's access is the sum of all their granted roles' privileges.

> **Access Control = DAC + RBAC:** Owners control their objects; privileges flow through roles to users.

#### Access control privileges

Privileges define access levels on specific objects (databases, schemas, tables, views, warehouses):

- `USAGE`: Use an object (warehouse, database, schema)
- `SELECT`: Query a table or view
- `INSERT`, `UPDATE`, `DELETE`: DML operations on tables
- `CREATE`: Create new objects in a schema or database

### Outline how privileges can be granted and revoked

Use `GRANT` and `REVOKE` commands:

- `GRANT <privileges> ON <object_type> <object_name> TO <role_name>`
- `REVOKE <privileges> ON <object_type> <object_name> FROM <role_name>`

> **Best Practice:** Grant privileges to roles, not directly to users.

### Explain role hierarchy and privilege inheritance

Roles can be granted to other roles, creating a hierarchy. A parent role inherits all privileges from its child roles. Example: granting `developer` and `analyst` roles to `data_engineer` gives `data_engineer` all privileges from both.

> **Role Hierarchy:** Roles granted to roles create inheritance. Parent roles accumulate child role privileges.

## Outline data governance capabilities in Snowflake

### Accounts

An account is the top-level entity—an isolated environment containing all data, users, and objects. Account-level settings include security policies and resource monitors.

### Organizations

Organizations link multiple Snowflake accounts for centralized management. A single organization administrator can monitor usage and manage billing across all linked accounts.

> **Organizations:** Centrally manage multiple Snowflake accounts. Single admin for usage monitoring and billing.

### Secure views

Secure views expose data without revealing the underlying table structure or view definition. Users see query results but cannot access the view's SQL logic.

> **Secure Views:** Hide view definition and underlying structure. Expose only query results.

### Secure functions

Secure UDFs hide their implementation logic. Users can call the function but cannot see the code, protecting sensitive business logic.

### Information schemas

Every database has an `INFORMATION_SCHEMA` containing metadata views about objects (tables, columns, views, procedures). Query these views to audit objects and understand data structure.

> **INFORMATION_SCHEMA:** Metadata views in every database. Query to audit objects and understand structure.

### Access history

The `ACCESS_HISTORY` view in `ACCOUNT_USAGE` logs all executed queries. Track which users accessed which tables and when.

#### Tracking read/write operations

Query `ACCESS_HISTORY` to answer questions like "Who queried the `employees` table in the last 30 days?"

> **ACCESS_HISTORY:** In ACCOUNT_USAGE schema. Tracks all query access—who, what, when.

### Overview of row/column-level security

- **Row Access Policies:** Control which rows users can see based on their role. Example: sales reps see only their region's data.
- **Masking Policies:** Mask sensitive column data based on role. Example: HR sees full SSN; analysts see only last four digits.

> **Row/Column Security:**
>
> - Row Access Policies: Filter rows by role
> - Masking Policies: Mask column values by role

### Object tags

Tags are key-value pairs attached to objects (tables, columns, warehouses). Use tags to classify data (e.g., PII columns), apply masking policies, and attribute costs to departments.

> **Object Tags:** Key-value metadata on objects. Use for data classification, policy application, and cost attribution.
