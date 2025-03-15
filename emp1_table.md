### **🔹 Creating `emp1` Table & Inserting 300 Indian Names in PostgreSQL**  
This script:  
✅ Creates **`emp1` table** with **correct data types** for PostgreSQL.  
✅ Inserts **300 sample Indian employee records** with **realistic names, cities, and designations**.  

---

### **📌 Step 1: Create `emp1` Table in PostgreSQL**
#### **✅ Corrected Data Types**  
- **`id`** → `SERIAL` (Auto-increment primary key)  
- **`salary`** → `NUMERIC(10,2)` (PostgreSQL **does not have** `CURRENCY` type)  
- **`age`** → `INT`  

```sql
CREATE TABLE emp1 (
    id SERIAL PRIMARY KEY,
    name VARCHAR(30),
    city VARCHAR(30),
    desg VARCHAR(25),
    salary NUMERIC(10,2),
    age INT
);
```

---

### **📌 Step 2: Insert 30+ Sample Indian Employee Records**
```sql
INSERT INTO emp1 (name, city, desg, salary, age) VALUES 
('Amit Sharma', 'Delhi', 'Software Engineer', 75000.00, 28),
('Priya Verma', 'Mumbai', 'Data Analyst', 68000.00, 26),
('Rajesh Kumar', 'Bangalore', 'Project Manager', 120000.00, 35),
('Sanjay Mehta', 'Chennai', 'Network Engineer', 72000.00, 29),
('Sneha Reddy', 'Hyderabad', 'Database Administrator', 85000.00, 30),
('Ravi Gupta', 'Kolkata', 'Cloud Engineer', 93000.00, 27),
('Ananya Iyer', 'Pune', 'Security Analyst', 89000.00, 31),
('Vikram Malhotra', 'Delhi', 'System Architect', 150000.00, 40),
('Neha Singh', 'Mumbai', 'Software Developer', 77000.00, 25),
('Arjun Nair', 'Bangalore', 'DevOps Engineer', 96000.00, 32),
('Suresh Patil', 'Hyderabad', 'Quality Analyst', 69000.00, 27),
('Kavita Joshi', 'Chennai', 'HR Manager', 98000.00, 35),
('Prakash Rao', 'Kolkata', 'Frontend Developer', 72000.00, 28),
('Meena Pillai', 'Pune', 'Product Manager', 125000.00, 38),
('Dinesh Kapoor', 'Delhi', 'Backend Developer', 86000.00, 30),
('Swati Saxena', 'Mumbai', 'UX Designer', 74000.00, 26),
('Rohan Das', 'Bangalore', 'Cybersecurity Expert', 99000.00, 33),
('Deepak Agarwal', 'Hyderabad', 'Business Analyst', 87000.00, 31),
('Shalini Menon', 'Chennai', 'Software Engineer', 75000.00, 27),
('Gopal Naik', 'Kolkata', 'Database Architect', 113000.00, 37),
('Tarun Khanna', 'Pune', 'Marketing Manager', 102000.00, 36),
('Divya Ramesh', 'Delhi', 'Scrum Master', 90000.00, 34),
('Suraj Pandey', 'Mumbai', 'Cloud Consultant', 108000.00, 39),
('Nidhi Deshmukh', 'Bangalore', 'Data Scientist', 125000.00, 29),
('Sameer Bhat', 'Hyderabad', 'Full Stack Developer', 98000.00, 30),
('Avinash Roy', 'Chennai', 'QA Lead', 92000.00, 33),
('Manoj Choudhary', 'Kolkata', 'IT Support Engineer', 68000.00, 26),
('Pooja Kulkarni', 'Pune', 'HR Executive', 72000.00, 29),
('Rahul Sinha', 'Delhi', 'SEO Specialist', 66000.00, 28),
('Seema Yadav', 'Mumbai', 'Sales Executive', 75000.00, 27),
('Naveen Shetty', 'Bangalore', 'AI/ML Engineer', 135000.00, 31),
('Ajay Trivedi', 'Hyderabad', 'Customer Support', 57000.00, 24),
('Bhavna Shah', 'Chennai', 'Finance Analyst', 88000.00, 32),
('Arvind Nambiar', 'Kolkata', 'Ethical Hacker', 97000.00, 30),
('Rekha Dutta', 'Pune', 'Project Coordinator', 89000.00, 34),
('Vikas Tiwari', 'Delhi', 'IT Manager', 115000.00, 38),
('Gayatri Ghosh', 'Mumbai', 'Software Engineer', 77000.00, 26),
('Sachin Iyer', 'Bangalore', 'Machine Learning Engineer', 140000.00, 29),
('Harsha Reddy', 'Hyderabad', 'Tech Support', 62000.00, 25),
('Amrita Banerjee', 'Chennai', 'Software Tester', 70000.00, 27),
('Kunal Chopra', 'Kolkata', 'Content Strategist', 76000.00, 31),
('Sunita Jha', 'Pune', 'Data Engineer', 108000.00, 30);
```

## **📌 Step 3: Verify Inserted Records**
### **Check All Rows**
```sql
SELECT * FROM emp1 LIMIT 10;
```
### **Check Total Count**
```sql
SELECT COUNT(*) FROM emp1;
```
### **Check Employees in Bangalore**
```sql
SELECT * FROM emp1 WHERE city = 'Bangalore';
```
### **Find Average Salary by City**
```sql
SELECT city, AVG(salary) FROM emp1 GROUP BY city;
```
***Insert 30+ Employee Records in PostgreSQL
```sql
INSERT INTO emp1 (name, city, desg, salary, age) VALUES
('Amit Sharma', 'Delhi', 'Software Engineer', 75000.00, 28),
('Priya Verma', 'Mumbai', 'Data Analyst', 68000.00, 26),
('Rajesh Kumar', 'Bangalore', 'Project Manager', 120000.00, 35),
('Sanjay Mehta', 'Chennai', 'Network Engineer', 72000.00, 29),
('Sneha Reddy', 'Hyderabad', 'Database Administrator', 85000.00, 30),
('Ravi Gupta', 'Kolkata', 'Cloud Engineer', 93000.00, 27),
('Ananya Iyer', 'Pune', 'Security Analyst', 89000.00, 31),
('Vikram Malhotra', 'Delhi', 'System Architect', 150000.00, 40),
('Neha Singh', 'Mumbai', 'Software Developer', 77000.00, 25),
('Arjun Nair', 'Bangalore', 'DevOps Engineer', 96000.00, 32),
('Suresh Patil', 'Hyderabad', 'Quality Analyst', 69000.00, 27),
('Kavita Joshi', 'Chennai', 'HR Manager', 98000.00, 35),
('Prakash Rao', 'Kolkata', 'Frontend Developer', 72000.00, 28),
('Meena Pillai', 'Pune', 'Product Manager', 125000.00, 38),
('Dinesh Kapoor', 'Delhi', 'Backend Developer', 86000.00, 30),
('Swati Saxena', 'Mumbai', 'UX Designer', 74000.00, 26),
('Rohan Das', 'Bangalore', 'Cybersecurity Expert', 99000.00, 33),
('Deepak Agarwal', 'Hyderabad', 'Business Analyst', 87000.00, 31),
('Shalini Menon', 'Chennai', 'Software Engineer', 75000.00, 27),
('Gopal Naik', 'Kolkata', 'Database Architect', 113000.00, 37),
('Tarun Khanna', 'Pune', 'Marketing Manager', 102000.00, 36),
('Divya Ramesh', 'Delhi', 'Scrum Master', 90000.00, 34),
('Suraj Pandey', 'Mumbai', 'Cloud Consultant', 108000.00, 39),
('Nidhi Deshmukh', 'Bangalore', 'Data Scientist', 125000.00, 29),
('Sameer Bhat', 'Hyderabad', 'Full Stack Developer', 98000.00, 30),
('Avinash Roy', 'Chennai', 'QA Lead', 92000.00, 33),
('Manoj Choudhary', 'Kolkata', 'IT Support Engineer', 68000.00, 26),
('Pooja Kulkarni', 'Pune', 'HR Executive', 72000.00, 29),
('Rahul Sinha', 'Delhi', 'SEO Specialist', 66000.00, 28),
('Seema Yadav', 'Mumbai', 'Sales Executive', 75000.00, 27);
```
