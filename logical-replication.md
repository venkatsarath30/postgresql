Here is a complete, clean, and practical script and step guide for setting up **PostgreSQL Logical Replication** between two servers:  
- **Master/Publisher:** 192.168.17.101  
- **Standby/Subscriber:** 192.168.17.102  
- **User:** repuser / Password: rep123  
- **Source DB:** logicadb  
- **Target DB:** repdb

# Objective: How to Set Up PostgreSQL Logical Replication

## 1. Master/Publisher Server Setup (192.168.17.101)

### 1.1 Edit `postgresql.conf`
Open your server’s `postgresql.conf` file and set:
```conf
listen_addresses = '*'
wal_level = logical
max_replication_slots = 10
max_wal_senders = 10
```

### 1.2 Edit `pg_hba.conf`
Add the Standby server’s IP to allow connections and replication for the repuser:
```
host    all            repuser    192.168.17.102/32     md5
host    replication    repuser    192.168.17.102/32     md5
```

### 1.3 Create/Alter Replication User
Connect as a superuser and create the user (or ensure superuser flag):
```sql
CREATE ROLE repuser WITH REPLICATION LOGIN ENCRYPTED PASSWORD 'rep123';
-- OR, if already exists:
ALTER ROLE repuser WITH SUPERUSER ENCRYPTED PASSWORD 'rep123';
\du  -- List users and privileges
```

### 1.4 Restart PostgreSQL
Apply config changes:
```shell
sudo systemctl restart postgresql
```

### 1.5 Create Database and Tables for Replication
```sql
CREATE DATABASE logicadb OWNER repuser;
\c logicadb

CREATE TABLE emp(id INT PRIMARY KEY, ename VARCHAR(30), desg VARCHAR(20));
INSERT INTO emp VALUES
    (1,'venkat','Sr DBA'),
    (2,'Sreenu Bro','Assoc Director'),
    (3,'Durgapathi','Assoc Ops Manager');

CREATE TABLE dept(id INT, dept VARCHAR(30), company VARCHAR(20));
INSERT INTO dept VALUES (1,'CIS','Cognizant');
```

### 1.6 Create Publication
```sql
-- To replicate all tables:
CREATE PUBLICATION mypub FOR ALL TABLES;
-- Or only specific tables:
-- CREATE PUBLICATION mypub FOR TABLE emp, dept;
\dRp  -- View publications
```

## 2. Standby/Subscriber Server Setup (192.168.17.102)

### 2.1 Edit `postgresql.conf` and `pg_hba.conf` (OPTIONAL)
**You only need to edit these files if you plan to use the standby as a publisher for cascading or future replication:**

#### `postgresql.conf`
```conf
listen_addresses = '*'
wal_level = logical                  # Needed for cascading logical replication
max_replication_slots = 10           # Adjust for expected subscribers
max_wal_senders = 10                 # Adjust for expected subscribers
```
#### `pg_hba.conf`
Add IP(s) of cascading standbys or admin hosts, e.g.:
```
host    all            repuser    /32     md5
host    replication    repuser    /32     md5
```
Reload or restart to apply:
```shell
sudo systemctl restart postgresql
```

### 2.2 Create Database and Matching Tables
```sql
CREATE DATABASE repdb OWNER repuser;
\c repdb

CREATE TABLE emp(id INT PRIMARY KEY, ename VARCHAR(30), desg VARCHAR(20));
CREATE TABLE dept(id INT, dept VARCHAR(30), company VARCHAR(20));
```

### 2.3 Create Subscription
```sql
CREATE SUBSCRIPTION mysub
  CONNECTION 'host=192.168.17.101 port=5432 dbname=logicadb user=repuser password=rep123'
  PUBLICATION mypub;
\dRs  -- View subscriptions
```

## 3. Test Replication

- Add/change records on the master and confirm they appear on the standby.

**Example:**
```sql
-- On master:
INSERT INTO emp VALUES (4, 'Santhosh', 'Cloud Manager');

-- On standby:
SELECT * FROM emp;
```

## 4. Publishing Schema Changes

- Add new tables FIRST on both master and subscriber.
- Alter publication and refresh subscription to include new tables:

**On Master:**
```sql
ALTER PUBLICATION mypub ADD TABLE skills;
CREATE TABLE skills(id INT PRIMARY KEY, name VARCHAR(30), status VARCHAR(20));
INSERT INTO skills VALUES (1,'java','technical'), (2,'dba','infrastructure');
```
**On Subscriber:**
```sql
CREATE TABLE skills(id INT PRIMARY KEY, name VARCHAR(30), status VARCHAR(20));
ALTER SUBSCRIPTION mysub REFRESH PUBLICATION;
SELECT * FROM skills;
```

## 5. Monitoring and Maintenance

- **Check status on master:**
    ```sql
    SELECT * FROM pg_replication_slots;
    SELECT * FROM pg_stat_replication;
    SELECT * FROM pg_publication;
    ```
- **Check status on subscriber:**
    ```sql
    SELECT * FROM pg_stat_subscription;
    SELECT * FROM pg_subscription;
    ```

- **Drop and recreate publications/subscriptions or replication slots as needed** if issues arise.

## 6. Notes & Best Practices

- **DDL changes (like CREATE TABLE, ADD COLUMN, etc.) are NOT replicated automatically**. Always run schema changes on both master and subscriber.
- Publication/subscription changes require “refresh” via `ALTER SUBSCRIPTION mysub REFRESH PUBLICATION;` on the subscriber when you add new tables to a publication.
- Logical replication is flexible for cross-platform (Linux/Windows) replication.
- Check logs and use monitoring queries for troubleshooting.

