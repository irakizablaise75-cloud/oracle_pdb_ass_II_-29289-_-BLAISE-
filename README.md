# Oracle PDB Management — Individual Assignment II

## Overview of Tasks

This assignment demonstrates practical skills in Oracle Multitenant Architecture using Oracle Database 21c. Four tasks were completed:

1. Created a new Pluggable Database (PDB) and a user inside it.
2. Created and deleted a temporary PDB to confirm full lifecycle management.
3. Verified Oracle Enterprise Manager (OEM) accessibility and dashboard.
4. Documented the full process in this repository with screenshot evidence.

## Oracle Environment Used

| Field | Details |
|---|---|
| Oracle Database | Oracle Database 21c |
| Feature | Multitenant Architecture (CDB/PDB) |
| Management Tool | Oracle Enterprise Manager (OEM) |
| Access | SQL Command Line (SQL*Plus) / SQL Developer |

## Multitenant Architecture

Oracle Multitenant uses a container database (CDB) to host one or more pluggable
databases (PDBs). The CDB root manages the common Oracle instance, while each PDB
provides an isolated logical database with its own users, objects, and service.
In this assignment, `ORCLPDB` is the existing PDB and `bl_pdb_29289` is the new
PDB created from `PDB$SEED`.

The main lifecycle is:

1. Connect to `CDB$ROOT` as a privileged administrator.
2. Create a PDB from `PDB$SEED` and open it.
3. Switch the session to the PDB before creating local users and objects.
4. Close and drop a temporary PDB when it is no longer required.

## Task 1: Create a New Pluggable Database

- **PDB name:** `bl_pdb_29289`
- **User created inside PDB:** `Blaise_plsqlauca_29289`
- The PDB was created successfully, opened, and the user was created inside it.

**Commands:**

```
sqlplus / as sysdba
CREATE PLUGGABLE DATABASE bl_pdb_29289
  ADMIN USER pdb_admin IDENTIFIED BY <password>
  FILE_NAME_CONVERT = ('/pdbseed/', '/bl_pdb_29289/');
ALTER PLUGGABLE DATABASE bl_pdb_29289 OPEN;
SHOW PDBS;

-- switch to the new PDB and create the user
ALTER SESSION SET CONTAINER = bl_pdb_29289;
CREATE USER Blaise_plsqlauca_29289 IDENTIFIED BY <password>;
GRANT CONNECT, RESOURCE TO Blaise_plsqlauca_29289;
```

The PDB administrator and the application user should be separate accounts. The
commands above use `pdb_admin` as the PDB administrator and
`Blaise_plsqlauca_29289` as the local user.

**Evidence:**

![Task 1 - PDB created and opened](screenshots/pdb_creation/01_create_pdb_and_open.png)

![Task 1 - PDB connection successful](screenshots/pdb_creation/02_pdb_connection_success.png)

## Task 2: Create and Delete a PDB

- **Temporary PDB name:** `bl_to_delete_pdb_29289`
- The temporary PDB was created, verified to exist, then dropped and confirmed removed.

**Commands:**

```
CREATE PLUGGABLE DATABASE bl_to_delete_pdb_29289
  ADMIN USER pdb_admin IDENTIFIED BY <password>
  FILE_NAME_CONVERT = ('/pdbseed/', '/bl_to_delete_pdb_29289/');
ALTER PLUGGABLE DATABASE bl_to_delete_pdb_29289 OPEN;
SHOW PDBS;

-- drop the temporary PDB (must be closed first)
ALTER PLUGGABLE DATABASE bl_to_delete_pdb_29289 CLOSE IMMEDIATE;
DROP PLUGGABLE DATABASE bl_to_delete_pdb_29289 INCLUDING DATAFILES;
SHOW PDBS;
```

**Evidence:**

![Task 2 - Temporary PDB created](screenshots/pdb_deletion/01_temp_pdb_created.png)

![Task 2 - Temporary PDB dropped](screenshots/pdb_deletion/02_temp_pdb_dropped.png)

## User Creation and Management

User administration must be performed in the intended PDB. The following checks
and management operations document the local-user lifecycle:

```sql
ALTER SESSION SET CONTAINER = bl_pdb_29289;

-- Confirm the user exists in this PDB
SELECT username, account_status, default_tablespace
FROM dba_users
WHERE username = 'BLAISE_PLSQLAUCA_29289';

-- Review the user's granted privileges
SELECT privilege
FROM dba_sys_privs
WHERE grantee = 'BLAISE_PLSQLAUCA_29289';

-- Example account-management operations
ALTER USER Blaise_plsqlauca_29289 ACCOUNT LOCK;
ALTER USER Blaise_plsqlauca_29289 ACCOUNT UNLOCK;
ALTER USER Blaise_plsqlauca_29289 IDENTIFIED BY <new-password>;
```

The screenshot evidence confirms creation and connection of the local user. The
queries and `ALTER USER` statements above are the documented management steps;
their output should be captured if the assignment requires evidence for each
operation.

## Task 3: Oracle Enterprise Manager (OEM)

- Oracle Enterprise Manager Database Express (OEM Express) is accessible for the
  Oracle environment. The supplied screenshot shows the SQL Monitor view with
  activity from `BL_PDB_29289`.

**How to access:**

```
-- OEM Express is enabled per PDB in Oracle 21c and is opened in a browser:
https://<hostname>:5500/em

-- Login with the PDB user created in Task 1 (Blaise_plsqlauca_29289) or SYSDBA credentials.
```

For a stronger OEM demonstration, capture the OEM landing page or database
target page, the PDB list/status, and the SQL Monitor view. The SQL Monitor
screenshot alone proves monitoring activity, but does not prove that the PDB
user successfully logged in or that all PDB administration was performed in
OEM.

**Evidence:**

![Task 3 - OEM SQL Monitor dashboard](screenshots/oem_dashboard/01_oem_sql_monitor_dashboard.png)

## Challenges Faced and Solutions

No significant challenges encountered.

## Security and Reproducibility Notes

- Never commit real database passwords. Use placeholders such as `<password>` in
  commands and redact passwords from screenshots.
- Replace `<hostname>` with the database host used in the local environment.
- Run the user-management queries in `bl_pdb_29289`, not in `CDB$ROOT`.

## Submission Details

- **Repository Link:** https://github.com/irakizablaise75-cloud/oracle_pdb_ass_II_-29289-_-BLAISE-
- **PDB Name Created:** `bl_pdb_29289`
- **Issues Encountered:** No