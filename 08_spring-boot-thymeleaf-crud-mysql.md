# Spring Boot CRUD Application with Thymeleaf & MySQL

## Overview
This guide covers building a complete CRUD (Create, Read, Update, Delete) application using Spring Boot, Thymeleaf templating engine, and MySQL database.

---

## Step 1: Database & Table Setup

### Create Database
```sql
CREATE DATABASE st2026;
USE st2026;

CREATE TABLE student (
    rollno INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    course VARCHAR(100) NOT NULL,
    branch VARCHAR(100) NOT NULL
);
```

**Tips:**
- Use `AUTO_INCREMENT` for primary key auto-generation
- Add `NOT NULL` constraints for required fields

---

## Step 2: Create Spring Boot Project

### Dependencies Required
- Spring Boot Dev Tools
- Spring Data JPA
- MySQL Driver
- Thymeleaf
- Spring Web

### Maven Dependency (pom.xml excerpt)
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

---

## Step 3: Application Configuration

### application.properties
```properties
server.port=8888
spring.datasource.url=jdbc:mysql://localhost:3306/st2026?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=your_password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect

spring.thymeleaf.cache=false
```

**Configuration Tips:**
- Set `ddl-auto=update` for development (creates tables automatically)
- Set `cache=false` for Thymeleaf during development for instant reload
- Use `serverTimezone=UTC` to avoid timezone issues

---

## Step 4: Project Package Structure

```
src/main/java/com/training/
├── Entity/
│   └── Student.java
├── Repository/
│   └── StudentRepository.java
├── Service/
│   └── StudentService.java
└── Controller/
    └── AppController.java

src/main/resources/templates/
└── showrecords.html
```

---

## Step 5: Create Entity Class

### Student.java
```java
package com.training.Entity;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;

@Entity
public class Student {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer rollno;
    
    private String name;
    private String course;
    private String branch;
    
    // Constructors
    public Student() {}
    
    public Student(Integer rollno, String name, String course, String branch) {
        this.rollno = rollno;
        this.name = name;
        this.course = course;
        this.branch = branch;
    }
    
    // Getters
    public Integer getRollno() {
        return rollno;
    }
    
    public String getName() {
        return name;
    }
    
    public String getCourse() {
        return course;
    }
    
    public String getBranch() {
        return branch;
    }
    
    // Setters
    public void setRollno(Integer rollno) {
        this.rollno = rollno;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public void setCourse(String course) {
        this.course = course;
    }
    
    public void setBranch(String branch) {
        this.branch = branch;
    }
}
```

**Important:**
- Use `@GeneratedValue(strategy = GenerationType.IDENTITY)` for auto-increment IDs
- Provide both default constructor and parameterized constructor for JPA

---

## Step 6: Create Repository Interface

### StudentRepository.java
```java
package com.training.Repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.training.Entity.Student;

@Repository
public interface StudentRepository extends JpaRepository<Student, Integer> {
    // JpaRepository provides findAll(), save(), delete(), findById() methods
}
```

**Tips:**
- `JpaRepository` automatically provides CRUD operations
- No need to implement methods - Spring provides them automatically
- Extend `CrudRepository` or `JpaRepository` to inherit database operations

---

## Step 7: Create Service Class

### StudentService.java
```java
package com.training.Service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.training.Entity.Student;
import com.training.Repository.StudentRepository;
import java.util.List;

@Service
public class StudentService {

    @Autowired
    private StudentRepository studentRepository;
    
    // Get all students
    public List<Student> showall() {
        return studentRepository.findAll();
    }
    
    // Add new student
    public Student addStudent(Student student) {
        return studentRepository.save(student);
    }
    
    // Update student
    public Student updateStudent(Student student) {
        return studentRepository.save(student);
    }
    
    // Delete student
    public void deleteStudent(Integer rollno) {
        studentRepository.deleteById(rollno);
    }
    
    // Get student by ID
    public Student getById(Integer rollno) {
        return studentRepository.findById(rollno).orElse(null);
    }
}
```

**Tips:**
- Service layer contains business logic
- Use `@Autowired` to inject Repository
- `save()` method works for both insert and update operations

---

## Step 8: Create Controller Class

### AppController.java
```java
package com.training.Controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.ModelAttribute;

import com.training.Entity.Student;
import com.training.Service.StudentService;

@Controller
public class AppController {

    @Autowired
    private StudentService stuserv;
    
    // Display all records
    @GetMapping("/showrecords")
    public String showrecords(Model model) {
        model.addAttribute("records", stuserv.showall());
        return "showrecords";
    }
    
    // Show add form
    @GetMapping("/addform")
    public String showAddForm(Model model) {
        model.addAttribute("student", new Student());
        return "addstudent";
    }
    
    // Save new student
    @PostMapping("/save")
    public String saveStudent(@ModelAttribute Student student) {
        stuserv.addStudent(student);
        return "redirect:/showrecords";
    }
    
    // Delete student
    @GetMapping("/delete/{rollno}")
    public String deleteStudent(@PathVariable Integer rollno) {
        stuserv.deleteStudent(rollno);
        return "redirect:/showrecords";
    }
    
    // Show edit form
    @GetMapping("/edit/{rollno}")
    public String editForm(@PathVariable Integer rollno, Model model) {
        Student student = stuserv.getById(rollno);
        model.addAttribute("student", student);
        return "editstudent";
    }
    
    // Update student
    @PostMapping("/update")
    public String updateStudent(@ModelAttribute Student student) {
        stuserv.updateStudent(student);
        return "redirect:/showrecords";
    }
}
```

**Controller Tips:**
- Use `@GetMapping` for displaying data
- Use `@PostMapping` for form submissions
- Use `@PathVariable` to extract values from URL
- Use `@ModelAttribute` to bind form data to objects
- `return "redirect:/showrecords"` redirects to another URL after operation

---

## Step 9: Create Thymeleaf Templates

### showrecords.html
```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Student Records</title>
</head>
<body>
    <h1>Students Records</h1>
    <a href="/addform">Add New Student</a>
    
    <table width="500px" border="2">
        <tr style="background-color: black; color: white;">
            <td>Roll Number</td>
            <td>Name</td>
            <td>Course</td>
            <td>Branch</td>
            <td>Actions</td>
        </tr>
        <tr th:each="row:${records}">
            <td th:text="${row.rollno}"></td>
            <td th:text="${row.name}"></td>
            <td th:text="${row.course}"></td>
            <td th:text="${row.branch}"></td>
            <td>
                <a th:href="@{/edit/{id}(id=${row.rollno})}">Edit</a>
                <a th:href="@{/delete/{id}(id=${row.rollno})}" onclick="return confirm('Delete?')">Delete</a>
            </td>
        </tr>
    </table>
</body>
</html>
```

**Thymeleaf Syntax:**
- `th:each="row:${records}"` - Loop through records
- `th:text="${row.name}"` - Display variable value
- `@{/edit/{id}(id=${row.rollno})}` - URL with parameter
- `${...}` - Access model attributes

### addstudent.html
```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Add Student</title>
</head>
<body>
    <h1>Add New Student</h1>
    <form th:action="@{/save}" th:object="${student}" method="POST">
        <label>Name:</label>
        <input type="text" th:field="*{name}" required><br>
        
        <label>Course:</label>
        <input type="text" th:field="*{course}" required><br>
        
        <label>Branch:</label>
        <input type="text" th:field="*{branch}" required><br>
        
        <button type="submit">Save</button>
        <a href="/showrecords">Cancel</a>
    </form>
</body>
</html>
```

### editstudent.html
```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Edit Student</title>
</head>
<body>
    <h1>Edit Student</h1>
    <form th:action="@{/update}" th:object="${student}" method="POST">
        <label>Roll Number:</label>
        <input type="text" th:field="*{rollno}" readonly><br>
        
        <label>Name:</label>
        <input type="text" th:field="*{name}" required><br>
        
        <label>Course:</label>
        <input type="text" th:field="*{course}" required><br>
        
        <label>Branch:</label>
        <input type="text" th:field="*{branch}" required><br>
        
        <button type="submit">Update</button>
        <a href="/showrecords">Cancel</a>
    </form>
</body>
</html>
```

**Form Binding Tips:**
- `th:field="*{name}"` - Binds to form object property
- `th:action="@{/save}"` - Sets form action URL
- `method="POST"` - Submits data via POST request
- `readonly` - Makes field read-only

---

## Running the Application

1. **Start MySQL server:**
   ```bash
   sudo service mysql start
   ```

2. **Run Spring Boot application:**
   ```bash
   mvn spring-boot:run
   ```

3. **Access the application:**
   - View records: http://localhost:8888/showrecords
   - Add student: http://localhost:8888/addform
   - Edit student: http://localhost:8888/edit/{rollno}
   - Delete student: http://localhost:8888/delete/{rollno}

---

## Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| **Table not created** | Check `ddl-auto=update` in properties and `@Entity` annotation on class |
| **Connection refused** | Verify MySQL is running and correct URL in properties |
| **Thymeleaf template not found** | Place HTML files in `src/main/resources/templates/` directory |
| **Column name not found** | Ensure entity field names match database column names |
| **Jar not found during import** | Run `mvn clean install` to download dependencies |

---

## Tips & Tricks

1. **Enable SQL logging for debugging:**
   ```properties
   spring.jpa.show-sql=true
   spring.jpa.properties.hibernate.format_sql=true
   ```

2. **Add timestamps to records:**
   ```java
   import java.time.LocalDateTime;
   import jakarta.persistence.Temporal;
   import jakarta.persistence.TemporalType;
   
   @Temporal(TemporalType.TIMESTAMP)
   private LocalDateTime createdAt = LocalDateTime.now();
   ```

3. **Check if record exists before update/delete:**
   ```java
   if (studentRepository.existsById(rollno)) {
       studentRepository.deleteById(rollno);
   }
   ```

4. **Custom query in Repository:**
   ```java
   @Query("SELECT s FROM Student s WHERE s.name LIKE %:name%")
   List<Student> searchByName(@Param("name") String name);
   ```

5. **Reload page after save using JavaScript:**
   ```html
   <script th:if="${success}">
       window.location.href = "/showrecords";
   </script>
   ```

---

## Summary

This Spring Boot CRUD application demonstrates:
- ✅ Entity creation with JPA annotations
- ✅ Repository for database access
- ✅ Service layer for business logic
- ✅ Controller for handling requests
- ✅ Thymeleaf templates for UI
- ✅ Database connectivity with MySQL
- ✅ CRUD operations (Create, Read, Update, Delete)
