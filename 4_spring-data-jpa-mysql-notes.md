# Spring Data JPA + Spring Boot Notes

This note explains how to use Spring Data JPA with MySQL, define an entity, build a repository, and expose data through a REST API.

## Definitions

- **Spring Data JPA**: a Spring module that simplifies data access by providing common repository abstractions and reducing boilerplate code for JPA.
- **MySQL Driver**: the JDBC driver that allows Spring Boot to connect to a MySQL database.
- **Entity**: a Java class mapped to a database table using JPA annotations such as `@Entity` and `@Table`.
- **Repository**: an interface that provides CRUD operations for entities, usually extending `CrudRepository` or `JpaRepository`.
- **Service**: a layer that contains business logic and coordinates repository calls.
- **Controller**: a Spring MVC class that handles HTTP requests and returns JSON responses.

---

## 1. Project Setup

### Required dependencies
- `spring-boot-starter-data-jpa`
- `mysql-connector-java`
- `spring-boot-starter-web`

### application.properties example
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/st2026?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

> Note: In production, avoid using `ddl-auto=update` and configure a secure password.

---

## 2. Database Table

Create the `student` table in the `st2026` database.

```sql
CREATE DATABASE IF NOT EXISTS st2026;
USE st2026;

CREATE TABLE student (
  rollno NUMERIC(10) PRIMARY KEY,
  name VARCHAR(60),
  course VARCHAR(80),
  branch VARCHAR(120)
);

INSERT INTO student VALUES
  (101, 'Anuj Kapoor', 'B.Tech', 'CSE'),
  (102, 'Priya Singh', 'M.Tech', 'ECE'),
  (103, 'Rohan Patel', 'BBA', 'Management'),
  (104, 'Sneha Sharma', 'MCA', 'IT'),
  (105, 'Pranjal', 'PhD', 'CS');
```

---

## 3. Package structure

Recommended packages:
- `com.training.controller`
- `com.training.entity`
- `com.training.repository`
- `com.training.service`

---

## 4. Entity class

Use a properly capitalized class name and JPA annotations.

```java
package com.training.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name = "student")
public class Student {

    @Id
    private Integer rollno;
    private String name;
    private String course;
    private String branch;

    public Student() {}

    public Student(Integer rollno, String name, String course, String branch) {
        this.rollno = rollno;
        this.name = name;
        this.course = course;
        this.branch = branch;
    }

    public Integer getRollno() {
        return rollno;
    }

    public void setRollno(Integer rollno) {
        this.rollno = rollno;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCourse() {
        return course;
    }

    public void setCourse(String course) {
        this.course = course;
    }

    public String getBranch() {
        return branch;
    }

    public void setBranch(String branch) {
        this.branch = branch;
    }
}
```

---

## 5. Repository interface

Extend `CrudRepository` or `JpaRepository` to get CRUD operations.

```java
package com.training.repository;

import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;

import com.training.entity.Student;

@Repository
public interface StudentRepository extends CrudRepository<Student, Integer> {
}
```

> Tip: Use `JpaRepository<Student, Integer>` when you need paging, sorting, or custom JPA methods.

---

## 6. Service layer

Use a service layer for business logic and repository access.

```java
package com.training.service;

import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.training.entity.Student;
import com.training.repository.StudentRepository;

@Service
public class StudentService {

    @Autowired
    private StudentRepository studentRepository;

    public Iterable<Student> getAllStudents() {
        return studentRepository.findAll();
    }

    public Optional<Student> getStudentByRollno(int rollno) {
        return studentRepository.findById(rollno);
    }
}
```

---

## 7. Controller layer

Create REST endpoints to expose student data.

```java
package com.training.controller;

import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.training.entity.Student;
import com.training.service.StudentService;

@RestController
@RequestMapping("/students")
public class StudentController {

    @Autowired
    private StudentService studentService;

    @GetMapping
    public Iterable<Student> getAllStudents() {
        return studentService.getAllStudents();
    }

    @GetMapping("/{rollno}")
    public Student getStudent(@PathVariable int rollno) {
        return studentService.getStudentByRollno(rollno)
                .orElseThrow(() -> new RuntimeException("Student not found"));
    }
}
```

> Better practice: return an HTTP 404 response rather than a plain `RuntimeException` in production.

---

## 8. Search API example

If you want a search endpoint, keep the controller response simple.

```java
@GetMapping("/search/{rollno}")
public Student searchByRollno(@PathVariable int rollno) {
    return studentService.getStudentByRollno(rollno)
            .orElseThrow(() -> new RuntimeException("Student not found"));
}
```

## 9. Response format

Spring Boot automatically serializes the returned `Student` object to JSON.

Example response from `/students`:
```json
[
  {
    "rollno": 101,
    "name": "Anuj Kapoor",
    "course": "B.Tech",
    "branch": "CSE"
  }
]
```

---

## 10. Notes and improvements
- Use proper naming conventions: `Student`, `StudentRepository`, `StudentService`, `StudentController`
- Store entities in `com.training.entity` and controllers in `com.training.controller`
- Use `JpaRepository` for more JPA features
- Avoid returning raw HTML strings from REST endpoints
- Use DTOs for custom response shapes if needed

