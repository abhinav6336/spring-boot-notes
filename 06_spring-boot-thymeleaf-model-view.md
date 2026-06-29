# Communication Between Thymeleaf and API

## Model Attributes

A controller can send data from the backend to a Thymeleaf template using the `Model` object.
This is typically done with a key-value pair, where the key becomes the variable name available in the view.

Syntax:

```java
model.addAttribute("keyname", "value");
```

Example:

```java
@Controller
public class AppController {

    @GetMapping("/home")
    public String home(Model model) {
        model.addAttribute("Name", "Dr. Dhananjay Sharma");
        model.addAttribute("Address", "Naini");
        model.addAttribute("Qualification", "Phd");
        return "home";
    }
}
```

Thymeleaf view:

```html
<html xmlns:th="http://www.thymeleaf.org">
  <body>
    <h1>United College</h1>
    <hr/>
    Name <span th:text="${Name}"></span><br/>
    Address <span th:text="${Address}"></span><br/>
    Qualification <span th:text="${Qualification}"></span>
  </body>
</html>
```

### Notes

- `model.addAttribute(...)` can pass strings, primitives, objects, lists, maps, or any Java bean.
- The `${...}` expression resolves values from the model based on the key name.
- Keys are case-sensitive in Thymeleaf expressions.

---

## Passing an Object to the View

Sometimes you want to pass a full Java object to the template instead of individual fields.
This is useful when your page needs multiple related values.

### 1. Create a Java bean (component)

```java
package com.training.Component;

import org.springframework.stereotype.Component;

@Component
public class Student {
    private int rollno;
    private String name;
    private String course;
    private String branch;

    public Student() {}

    public Student(int rollno, String name, String course, String branch) {
        this.rollno = rollno;
        this.name = name;
        this.course = course;
        this.branch = branch;
    }

    public int getRollno() {
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

    public void setRollno(int rollno) {
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

> Note: `@Component` is optional for a simple data holder. A plain POJO works fine when created directly inside the controller.

### 2. Send the object from the controller

```java
@Controller
public class AppController {

    @GetMapping("/home")
    public String home(Model model) {
        Student student = new Student(101, "Anuj Kapoor", "B.Tech.", "CS");
        model.addAttribute("student", student);
        return "home";
    }
}
```

### 3. Use the object in the view page

```html
<html xmlns:th="http://www.thymeleaf.org">
  <body>
    <h1>United College</h1>
    <hr/>
    <div th:object="${student}">
      Roll Number <span th:text="*{rollno}"></span><br/>
      Name <span th:text="*{name}"></span><br/>
      Course <span th:text="*{course}"></span><br/>
      Branch <span th:text="*{branch}"></span><br/>
    </div>
  </body>
</html>
```

### Why use `th:object`?

- `th:object="${student}"` sets the current object context.
- Inside this block, `*{...}` resolves against the `student` object.
- This is especially useful in forms, where field binding and validation can use the same object context.

---

## Message Expression

Thymeleaf supports internationalization and externalized text using message properties.
Put the messages file under `src/main/resources` and name it `messages.properties`.

Example `messages.properties`:

```properties
app.title=United College
dev.info=Dhananjay Kr Sharma
```

Use these values in the HTML template:

```html
<h1 th:text="#{app.title}"></h1>
<h5 th:text="#{dev.info}"></h5>
```

### Extra details

- Spring Boot automatically loads `messages.properties` from `src/main/resources` by default.
- For multiple languages, add files like `messages_en.properties`, `messages_fr.properties`, etc.
- Message expressions are good for labels, headings, validation messages, and UI text.

---

## Link Expression

Link expressions create context-relative URLs in Thymeleaf templates.
They are useful for navigation links, actions, and generating URLs safely.

Example controller mappings:

```java
@GetMapping("/about")
public String about() {
    return "about";
}

@GetMapping("/contact")
public String contact() {
    return "contact";
}
```

Example links:

```html
<a th:href="@{/about}">About</a><br/>
<a th:href="@{/contact}">Contact</a><br/>
<a th:href="@{/}">Go Back</a><br/>
```

### Useful tips

- `@{/about}` produces a URL relative to the current application context.
- You can add query parameters as `@{/search(q=${query})}`.
- For path variables, use `@{/user/{id}(id=${userId})}`.
- Link expressions help avoid hard-coded URLs and improve portability.
