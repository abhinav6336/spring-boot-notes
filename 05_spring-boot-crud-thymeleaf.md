# Spring Boot Notes: CRUD, Custom Finder Methods, and Thymeleaf

## CRUD Overview
CRUD stands for:
- **Create**
- **Read**
- **Update**
- **Delete**

In REST APIs, these map to HTTP methods:
- `GET` for read operations
- `POST` for create operations
- `PUT` or `PATCH` for update operations
- `DELETE` for delete operations

### API Testing Tool
- Use **Postman** to test REST endpoints.
- Common actions:
  - Show all records
  - Search records
  - Insert record
  - Update record
  - Delete record

---

## Service Layer Example
Use the service layer to isolate business logic from controllers.

```java
public student savedata(student s) {
    return sturepo.save(s);
}

public void deletedata(int rollno) {
    sturepo.deleteById(rollno);
}

public student updatedata(student s) {
    return sturepo.save(s);
}
```

> Note: With Spring Data JPA, `save()` can be used for both insert and update operations.

---

## Insert API

### Controller Method

```java
@PostMapping("/insert/{rollno}/{name}/{course}/{branch}")
public student insert(
        @PathVariable int rollno,
        @PathVariable String name,
        @PathVariable String course,
        @PathVariable String branch) {
    student s = new student(rollno, name, course, branch);
    return stuserv.savedata(s);
}
```

### Test URL

```
http://localhost:8888/insert/106/Dr.%20Dhananjay%20Sharma/Ph.d./CS
```

> Tip: Encode spaces in URLs or use a request body instead of path variables for complex strings.

---

## Delete API

### Service Method

```java
public void deletedata(int rollno) {
    sturepo.deleteById(rollno);
}
```

### Controller Method

```java
@DeleteMapping("/deleterecord/{rollno}")
public void deleterecord(@PathVariable int rollno) {
    stuserv.deletedata(rollno);
}
```

### Test URL

```
http://localhost:8888/deleterecord/101
```

---

## Update API

### Service Method

```java
public student updatedata(student s) {
    return sturepo.save(s);
}
```

### Controller Method

```java
@PutMapping("/updateapi/{rollno}/{name}/{course}/{branch}")
public student updateapi(
        @PathVariable int rollno,
        @PathVariable String name,
        @PathVariable String course,
        @PathVariable String branch) {
    student s = new student(rollno, name, course, branch);
    return stuserv.updatedata(s);
}
```

### Test URL

```
http://localhost:8888/updateapi/104/Komal%20Yadav/MCA/CS
```

---

## Custom Finder Methods with Spring Data JPA
Spring Data lets you define query methods in repository interfaces by using the method name.

### Common patterns
- `findById(id)`
- `findBy<FieldName>(value)`

### Repository Example

```java
@Repository
public interface StudentRepository extends CrudRepository<student, Integer> {
    List<student> findByname(String name);
    List<student> findBycourse(String course);
    List<student> findBybranch(String branch);
}
```

### Service Methods

```java
public List<student> searchbyname(String name) {
    return sturepo.findByname(name);
}

public List<student> searchbycourse(String course) {
    return sturepo.findBycourse(course);
}

public List<student> searchbybranch(String branch) {
    return sturepo.findBybranch(branch);
}
```

### Controller Methods

```java
@GetMapping("/findbynameapi/{name}")
public List<student> findbynameapi(@PathVariable String name) {
    return stuserv.searchbyname(name);
}

@GetMapping("/findbycourseapi/{cname}")
public List<student> findbycourseapi(@PathVariable String cname) {
    return stuserv.searchbycourse(cname);
}

@GetMapping("/findbybranchapi/{bname}")
public List<student> findbybranchapi(@PathVariable String bname) {
    return stuserv.searchbybranch(bname);
}
```

---

## Thymeleaf Basics
Thymeleaf is the default template engine in Spring Boot for rendering HTML views.

### Key points
- Use the `thymeleaf` dependency in your project.
- Put templates in `src/main/resources/templates`.
- Use `th` namespace in HTML files.

### Example template: `home.html`

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <body>
    <h1>United College</h1>
    <hr/>
  </body>
</html>
```

### Controller Example

```java
@Controller
public class AppController {
    @GetMapping("/home")
    public String home() {
        return "home";
    }
}
```

### Test URL

```
http://localhost:8888/home
```


