# Spring Boot Database and API Notes

## 1. Request Parameters in Spring Boot

Spring Boot uses `@RequestParam` to read query string values from URLs.

### 1.1 `@RequestParam` example
```java
@GetMapping("/sayhello")
public String sayHello(
        @RequestParam String name,
        @RequestParam String course) {
    return "Hello " + name + "<br>" + "Your Course is " + course;
}
```

### 1.2 Example request
```
http://localhost:8888/sayhello?name=Dhananjay&course=PhD
```

### 1.3 Optional parameters and default values
```java
@GetMapping("/greet")
public String greet(
        @RequestParam(required = false, defaultValue = "Guest") String name) {
    return "Hello " + name;
}
```

### 1.4 Path variable comparison
Use `@PathVariable` when the value is part of the endpoint path.
```java
@GetMapping("/student/{id}")
public String getStudent(@PathVariable int id) {
    return "Student id=" + id;
}
```

---

## 2. Working with Databases in Spring Boot

### 2.1 Common database options
- MySQL: relational database with SQL support
- MongoDB: document database for NoSQL storage

### 2.2 Typical Spring Boot setup for MySQL
Add these dependencies in `pom.xml`:
- `spring-boot-starter-data-jpa`
- `mysql-connector-java`

Configure `application.properties`:
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/st2026?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

> Note: `spring.jpa.hibernate.ddl-auto=update` automatically updates tables in development. Use `validate` or remove it for production.

---

## 3. SQL Command Categories

### 3.1 DDL — Data Definition Language
Used to create or modify database structure.
- `CREATE TABLE`
- `ALTER TABLE`
- `DROP TABLE`

### 3.2 DML — Data Manipulation Language
Used to insert, update, delete, or query data.
- `INSERT`
- `UPDATE`
- `DELETE`
- `SELECT`

### 3.3 DQL — Data Query Language
- `SELECT`

### 3.4 TCL / DCL (optional)
- Transaction control: `COMMIT`, `ROLLBACK`
- Privilege control: `GRANT`, `REVOKE`

---

## 4. DDL Examples

### 4.1 Create table
```sql
CREATE TABLE emp (
  empid NUMERIC(5) PRIMARY KEY,
  ename VARCHAR(40),
  edesig VARCHAR(60),
  salary NUMERIC(10,2)
);
```

### 4.2 Alter table
```sql
ALTER TABLE emp
  MODIFY ename VARCHAR(80);

ALTER TABLE emp
  ADD phone_no VARCHAR(10);
```

### 4.3 Drop table
```sql
DROP TABLE emp;
```

### 4.4 Inspect table structure
```sql
DESCRIBE emp;
-- or
SHOW COLUMNS FROM emp;
```

---

## 5. DML Examples

### 5.1 Insert records
```sql
INSERT INTO emp VALUES (101, 'Anuj Kapoor', 'Manager', 120000);
INSERT INTO emp(empid, ename, edesig) VALUES (102, 'Pankaj Singh', 'Developer');
```

### 5.2 Update records
```sql
UPDATE emp
SET salary = 130000
WHERE empid = 101;
```

### 5.3 Delete records
```sql
DELETE FROM emp
WHERE empid = 102;
```

### 5.4 Query records
```sql
SELECT * FROM emp;
SELECT ename, salary FROM emp WHERE salary > 50000;
```

---

## 6. Practice: Employee Records (`EMPREC`)

### 6.1 Table structure
| Field | Type | Description |
|-------|------|-------------|
| empid | NUMERIC(5) | Employee ID (Primary Key) |
| ename | VARCHAR(60) | Employee Name |
| basic | NUMERIC(10) | Basic Salary |
| ta | NUMERIC(10) | Travel Allowance (10% of basic) |
| da | NUMERIC(10) | Dearness Allowance (15% of basic) |
| hra | NUMERIC(10) | House Rent Allowance (6% of basic) |

### 6.2 Create table
```sql
CREATE TABLE emprec (
  empid NUMERIC(5) PRIMARY KEY,
  ename VARCHAR(60),
  basic NUMERIC(10),
  ta NUMERIC(10),
  da NUMERIC(10),
  hra NUMERIC(10)
);
```

### 6.3 Insert sample records
```sql
INSERT INTO emprec(empid, ename, basic) VALUES (101, 'Anuj', 70000);
INSERT INTO emprec(empid, ename, basic) VALUES (102, 'Alok', 50000);
INSERT INTO emprec(empid, ename, basic) VALUES (103, 'Anu', 60000);
INSERT INTO emprec(empid, ename, basic) VALUES (104, 'Amit', 30000);
INSERT INTO emprec(empid, ename, basic) VALUES (105, 'Akash', 40000);
```

### 6.4 Calculate allowances
```sql
UPDATE emprec
SET ta = basic * 0.10,
    da = basic * 0.15,
    hra = basic * 0.06;
```

### 6.5 View final records
```sql
SELECT empid, ename, basic, ta, da, hra
FROM emprec;
```

---

## 7. Quick Spring Boot API tip

When returning Java objects from a controller, use `@RestController` and return model classes.

Example:
```java
@RestController
public class EmpController {

    @GetMapping("/empinfo")
    public Emp empInfo() {
        return new Emp(101, "Anuj Kapoor", "Manager", 120000);
    }
}
```

This keeps your API clean, typed, and easy to consume from clients.

Use UPDATE command to calculate ta, da, and hra:

```sql
UPDATE emprec SET 
  ta = basic * 10 / 100,
  da = basic * 15 / 100,
  hra = basic * 6 / 100;
```

### Step 3: View All Records

```sql
SELECT * FROM emprec;
```





