### **Restoring a Database in Ora2Pg (Oracle to PostgreSQL Migration Tool)**  

Ora2Pg is an open-source tool that migrates Oracle databases to PostgreSQL. The restore process involves:  
1. **Exporting the Oracle schema/data using Ora2Pg**  
2. **Restoring the exported data into PostgreSQL**  

---

## **🔹 Step 1: Prepare PostgreSQL for Restoration**
Before restoring the database, make sure:  
✅ PostgreSQL is installed and running  
✅ A database is created for the import  
✅ Necessary extensions (like `uuid-ossp` for UUID columns) are enabled  

### **Create the Target Database**
```sh
sudo -u postgres psql
```
```sql
CREATE DATABASE my_pg_database;
\q
```

---

## **🔹 Step 2: Export Data from Oracle using Ora2Pg**
If not already done, export the Oracle schema and data using Ora2Pg:  
```sh
ora2pg -c ora2pg.conf -t TABLE -o export_schema.sql
ora2pg -c ora2pg.conf -t COPY -o export_data.sql
```

---

## **🔹 Step 3: Restore the Schema into PostgreSQL**
Run the exported schema SQL file in PostgreSQL:  
```sh
psql -U postgres -d my_pg_database -f export_schema.sql
```

Verify that tables are created:
```sh
psql -U postgres -d my_pg_database -c "\dt"
```

---

## **🔹 Step 4: Restore Data into PostgreSQL**
If the data was exported using the COPY method, use:  
```sh
psql -U postgres -d my_pg_database -f export_data.sql
```
If the data was exported in INSERT format, use:  
```sh
psql -U postgres -d my_pg_database -f export_data.sql --single-transaction
```

---

## **🔹 Step 5: Verify Data Restoration**
Check the number of rows in the restored tables:
```sh
psql -U postgres -d my_pg_database -c "SELECT COUNT(*) FROM my_table;"
```

If indexes were disabled during migration, rebuild them:
```sh
REINDEX DATABASE my_pg_database;
```

---

## **🔹 Step 6: Perform Post-Restore Optimizations**
1. **Run ANALYZE** to update PostgreSQL statistics:  
   ```sh
   vacuumdb -U postgres -d my_pg_database --analyze
   ```
2. **Rebuild Indexes if needed**:  
   ```sql
   REINDEX DATABASE my_pg_database;
   ```

---
### **✅ Summary**
1️⃣ **Export Oracle schema & data** using Ora2Pg  
2️⃣ **Create a PostgreSQL database** as a target  
3️⃣ **Restore schema** using `psql -f export_schema.sql`  
4️⃣ **Restore data** using `psql -f export_data.sql`  
5️⃣ **Verify data & optimize PostgreSQL performance**  

---

If you encounter any issues during the restoration process in Ora2Pg, here are some troubleshooting tips:

---

### **🔹 Additional Steps for a Smooth Migration**
#### **1️⃣ Check Compatibility Before Migration**
Before restoring data, check for compatibility issues between Oracle and PostgreSQL:
```sh
ora2pg -c ora2pg.conf -t TEST -o compatibility_report.txt
```
Review the generated report (`compatibility_report.txt`) for any potential issues.

---

#### **2️⃣ Handle Foreign Keys and Constraints**
To prevent constraint violations during data import, you can disable foreign keys and indexes:
```sql
ALTER TABLE my_table DISABLE TRIGGER ALL;
```
After restoring data, re-enable them:
```sql
ALTER TABLE my_table ENABLE TRIGGER ALL;
```

---

#### **3️⃣ Restore with Parallel Processing**
For large datasets, you can speed up restoration using `pg_restore`:
```sh
pg_restore -U postgres -d my_pg_database -j 4 export_data.sql
```
Where `-j 4` enables 4 parallel jobs.

---

#### **4️⃣ Handle Large Tables with Partitioning**
If you’re dealing with very large tables, consider migrating them in chunks:
```sql
COPY my_large_table FROM 'data_part1.csv' CSV HEADER;
COPY my_large_table FROM 'data_part2.csv' CSV HEADER;
```

---

### **🔹 Troubleshooting Common Issues**
| **Issue** | **Solution** |
|-----------|-------------|
| **Data type mismatch errors** | Check data types in `export_schema.sql` and modify them if needed. |
| **Primary key violations** | Remove duplicates before restoring using `DISTINCT` or `ON CONFLICT`. |
| **Foreign key constraint failures** | Load parent tables first, disable constraints, and enable them post-import. |
| **Slow performance** | Use `pg_restore -j 4` for parallel processing and run `VACUUM ANALYZE`. |
| **Encoding issues (UTF-8 conflicts)** | Ensure both databases use the same encoding (`SHOW server_encoding;`). |

---

### **✅ Final Post-Restore Checks**
1️⃣ Validate table counts:
```sql
SELECT relname, n_live_tup FROM pg_stat_user_tables;
```
2️⃣ Ensure sequences are in sync:
```sql
SELECT pg_catalog.setval(pg_get_serial_sequence('my_table', 'id'), MAX(id)) FROM my_table;
```
3️⃣ Run a full database analysis:
```sh
vacuumdb -U postgres -d my_pg_database --full --analyze
```
---

### **🚀 Summary**
🔹 Ensure compatibility before migration  
🔹 Optimize the restoration with indexes & constraints  
🔹 Use parallel processing (`pg_restore -j`) for large datasets  
🔹 Troubleshoot common issues like foreign keys and encoding  
🔹 Perform post-restore checks for data integrity  

