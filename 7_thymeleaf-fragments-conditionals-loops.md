# Thymeleaf Fragments, Conditionals, and Loops

## Overview
This guide covers advanced Thymeleaf features including:
- Template fragments for code reusability
- Conditional rendering with if/unless
- Switch-case statements
- Looping with th:each
- CSS styling and resource management

---

## Part 1: Thymeleaf Fragments (Reusable Components)

### What are Fragments?
Fragments are reusable template pieces (header, menu, footer) that can be included in multiple pages, reducing code duplication.

### Step 1: Create common.html (Fragment File)

Create file: `src/main/resources/templates/common.html`

```html
<html xmlns:th="http://www.thymeleaf.org">
<body>
    <!-- Header Fragment -->
    <div th:fragment="header">
        <h1 style="text-align: center; color: #333;">United College</h1>
    </div>
    
    <!-- Navigation Menu Fragment -->
    <div th:fragment="menu">
        <hr/>
        <nav style="text-align: center; margin: 10px 0;">
            <a th:href="@{/home}" style="margin: 0 10px;">Home</a>
            <a th:href="@{/about}" style="margin: 0 10px;">About</a>
            <a th:href="@{/contact}" style="margin: 0 10px;">Contact</a>
            <a th:href="@{/register}" style="margin: 0 10px;">Register</a>
            <a th:href="@{/}" style="margin: 0 10px;">Admin</a>
        </nav>
        <hr/>
    </div>
    
    <!-- Footer Fragment -->
    <div th:fragment="footer">
        <footer style="background-color: #333; color: white; text-align: center; padding: 10px;">
            <p>Developed By Team Springboot &copy; 2024</p>
        </footer>
    </div>
</body>
</html>
```

**Fragment Tips:**
- Use `th:fragment="name"` to define a fragment
- Fragment names should be meaningful (header, menu, footer)
- Keep fragments focused on single responsibility
- Use consistent styling across fragments

### Step 2: Create home.html (Using Fragments)

Create file: `src/main/resources/templates/home.html`

```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Home - United College</title>
</head>
<body>
    <table width="1200px" align="center" border="0">
        <!-- Include Header Fragment -->
        <tr>
            <td>
                <div th:replace="~{common::header}"></div>
            </td>
        </tr>
        
        <!-- Include Menu Fragment -->
        <tr>
            <td>
                <div th:replace="~{common::menu}"></div>
            </td>
        </tr>
        
        <!-- Main Content -->
        <tr>
            <td style="padding: 20px;">
                <h2>Welcome to Home Page</h2>
                <p>This is the main content area of the home page.</p>
                <p>Add your content here...</p>
            </td>
        </tr>
        
        <!-- Include Footer Fragment -->
        <tr>
            <td>
                <div th:replace="~{common::footer}"></div>
            </td>
        </tr>
    </table>
</body>
</html>
```

**Fragment Usage Syntax:**
- `th:replace="~{common::header}"` - Replace entire element with fragment
- `th:include="~{common::header}"` - Include fragment inside element (deprecated)
- Fragment path format: `~{templatename::fragmentname}`

### Step 3: Create Additional Pages (about.html, contact.html, register.html)

Create file: `src/main/resources/templates/about.html`

```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>About - United College</title>
</head>
<body>
    <table width="1200px" align="center" border="0">
        <tr><td><div th:replace="~{common::header}"></div></td></tr>
        <tr><td><div th:replace="~{common::menu}"></div></td></tr>
        
        <tr>
            <td style="padding: 20px;">
                <h2>About Us</h2>
                <p>United College is dedicated to providing quality education and fostering innovation.</p>
                <p>Our mission is to create future leaders and innovators.</p>
            </td>
        </tr>
        
        <tr><td><div th:replace="~{common::footer}"></div></td></tr>
    </table>
</body>
</html>
```

**Fragment Best Practices:**
- Reuse fragments consistently across pages
- Update fragment once to reflect changes everywhere
- Keep fragments generic for better reusability
- Avoid fragment-specific styling

### Step 4: Define Controllers

```java
package com.training.Controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class AppController {
    
    // Home page
    @GetMapping("/home")
    public String home() {
        return "home";
    }
    
    // About page
    @GetMapping("/about")
    public String about() {
        return "about";
    }
    
    // Contact page
    @GetMapping("/contact")
    public String contact() {
        return "contact";
    }
    
    // Register page
    @GetMapping("/register")
    public String register() {
        return "register";
    }
    
    // Admin/Root page
    @GetMapping("/")
    public String admin() {
        return "admin";
    }
}
```

---

## Part 2: Using CSS for Styling

### Step 1: Create CSS File

Create folder: `src/main/resources/static/css/`
Create file: `pagestyle.css`

```css
/* Global Styles */
body {
    font-family: Arial, sans-serif;
    background-color: #f0f0f0;
    margin: 0;
    padding: 0;
}

/* Table Styling */
table {
    background-color: white;
    border-collapse: collapse;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

/* Header Class */
.header {
    font-family: Impact, sans-serif;
    color: maroon;
    font-size: 45px;
    text-align: center;
    margin: 20px 0;
}

/* Footer Class */
.footer {
    background-color: black;
    color: white;
    text-align: center;
    padding: 15px;
    margin-top: 20px;
}

/* Navigation Styling */
.navbar {
    background-color: #333;
    padding: 0;
    margin: 0;
}

.navbar a {
    color: white;
    text-decoration: none;
    padding: 12px 20px;
    display: inline-block;
}

.navbar a:hover {
    background-color: #555;
}

/* Content Area */
.content {
    padding: 20px;
    background-color: white;
    margin: 20px 0;
}

/* Button Styling */
.btn {
    padding: 10px 20px;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.btn:hover {
    background-color: #45a049;
}
```

### Step 2: Link CSS in HTML Template

Add this in the `<head>` section of your HTML files:

```html
<head>
    <title>Page Title</title>
    <link rel="stylesheet" th:href="@{/css/pagestyle.css}" type="text/css" />
</head>
```

**CSS Tips:**
- Use `th:href="@{/css/filename.css}"` to link CSS from static folder
- Organize CSS in separate files for better maintainability
- Use meaningful class names for styling
- Keep CSS in separate files, not inline

### Step 3: Embedding Images

Create folder: `src/main/resources/static/images/`
Place your images in this folder.

In your HTML template:

```html
<img th:src="@{/images/banner1.png}" width="100%" alt="Banner" />
<img th:src="@{/images/logo.png}" width="200px" alt="Logo" />
```

**Image Tips:**
- Use `th:src="@{/images/filename}"` to reference images
- Always add meaningful `alt` text for accessibility
- Optimize image sizes for web
- Keep images in static/images folder

---

## Part 3: Conditional Rendering

### Using th:if

Display content only when condition is true.

```html
<div th:if="${condition}">
    <p>This shows when condition is true</p>
</div>
```

### Using th:unless

Display content only when condition is false.

```html
<div th:unless="${condition}">
    <p>This shows when condition is false</p>
</div>
```

### Example: Role-Based Display

**Controller:**
```java
@GetMapping("/home")
public String home(Model model) {
    model.addAttribute("username", "Dhananjay");
    model.addAttribute("usertype", "admin");
    return "home";
}
```

**HTML Template:**
```html
<!-- Show admin message if user is admin -->
<div th:if="${usertype == 'admin'}">
    <p style="color: green; font-weight: bold;">
        Welcome Admin <span th:text="${username}"></span>!
    </p>
</div>

<!-- Show guest message if user is not admin -->
<div th:unless="${usertype == 'admin'}">
    <p style="color: red;">
        Welcome Guest... Please log in.
    </p>
</div>
```

**Conditional Tips:**
- Use `==` for equality, `!=` for inequality
- Use `&&` for AND, `||` for OR
- Always use proper comparison operators
- Test all condition branches

### Common Conditional Expressions

```html
<!-- Check if list is empty -->
<div th:if="${records.isEmpty()}">
    <p>No records found</p>
</div>

<!-- Check if variable is not null -->
<div th:if="${user != null}">
    <p>User is logged in</p>
</div>

<!-- Complex condition -->
<div th:if="${usertype == 'admin' && status == 'active'}">
    <p>Active Admin User</p>
</div>
```

---

## Part 4: Switch-Case Statements

### Syntax

```html
<div th:switch="${expression}">
    <p th:case="'value-1'">Statement 1</p>
    <p th:case="'value-2'">Statement 2</p>
    <p th:case="*">Default Statement</p>
</div>
```

### Example: User Type Based Display

**Controller:**
```java
@GetMapping("/dashboard")
public String dashboard(Model model) {
    model.addAttribute("username", "Dhananjay");
    model.addAttribute("usertype", "admin");
    return "dashboard";
}
```

**HTML Template:**
```html
<div th:switch="${usertype}">
    <p th:case="'admin'" style="color: green; font-weight: bold;">
        Welcome Admin <span th:text="${username}"></span>! 
        You have full access to the system.
    </p>
    
    <p th:case="'staff'" style="color: blue;">
        Welcome Staff Member <span th:text="${username}"></span>! 
        You can view assigned records only.
    </p>
    
    <p th:case="'student'" style="color: orange;">
        Welcome Student <span th:text="${username}"></span>! 
        View your grades and assignments.
    </p>
    
    <p th:case="*" style="color: red;">
        Unknown User Type: <span th:text="${usertype}"></span>
    </p>
</div>
```

**Switch-Case Tips:**
- Use `th:case="*"` for default case
- Case values must be in single quotes ('value')
- More readable than multiple if-else statements
- Useful for role-based UI

---

## Part 5: Looping with th:each

### Syntax

```html
<div th:each="item : ${collection}">
    <p th:text="${item.property}"></p>
</div>
```

### Step 1: Create Component Class

```java
package com.training.Component;

public class Student {
    private int rollno;
    private String name;
    private String course;
    private String branch;
    
    // Default constructor
    public Student() {}
    
    // Parameterized constructor
    public Student(int rollno, String name, String course, String branch) {
        this.rollno = rollno;
        this.name = name;
        this.course = course;
        this.branch = branch;
    }
    
    // Getters
    public int getRollno() { return rollno; }
    public String getName() { return name; }
    public String getCourse() { return course; }
    public String getBranch() { return branch; }
    
    // Setters
    public void setRollno(int rollno) { this.rollno = rollno; }
    public void setName(String name) { this.name = name; }
    public void setCourse(String course) { this.course = course; }
    public void setBranch(String branch) { this.branch = branch; }
}
```

### Step 2: Pass Data from Controller

```java
@GetMapping("/home")
public String home(Model model) {
    List<Student> records = new ArrayList<>();
    records.add(new Student(101, "Anuj", "B.Tech", "CS"));
    records.add(new Student(102, "Pankaj", "M.Tech", "CS"));
    records.add(new Student(103, "Manoj", "B.Tech", "IT"));
    records.add(new Student(104, "Komal", "MCA", "CS"));
    records.add(new Student(105, "Anu", "B.Tech", "AIML"));
    
    model.addAttribute("records", records);
    return "home";
}
```

### Step 3: Display Records Using Loop

```html
<table width="600px" border="2">
    <thead>
        <tr style="background-color: #333; color: white;">
            <th>Roll Number</th>
            <th>Name</th>
            <th>Course</th>
            <th>Branch</th>
        </tr>
    </thead>
    
    <tbody>
        <!-- Empty list handling -->
        <tr th:if="${records.isEmpty()}">
            <td colspan="4" style="text-align: center; color: #999;">
                No records found
            </td>
        </tr>
        
        <!-- Loop through records -->
        <tr th:each="row : ${records}" th:classappend="${rowStat.odd} ? 'odd-row' : 'even-row'">
            <td th:text="${row.rollno}"></td>
            <td th:text="${row.name}"></td>
            <td th:text="${row.course}"></td>
            <td th:text="${row.branch}"></td>
        </tr>
    </tbody>
</table>
```

**Loop Variable Tips:**
- Use `${rowStat.index}` for 0-based index
- Use `${rowStat.count}` for 1-based index
- Use `${rowStat.size}` for total items
- Use `${rowStat.first}` to check if first item
- Use `${rowStat.last}` to check if last item
- Use `${rowStat.odd}` and `${rowStat.even}` for alternating rows

### Advanced Loop Example with Actions

```html
<table width="800px" border="2">
    <thead>
        <tr style="background-color: #333; color: white;">
            <th>S.No</th>
            <th>Roll Number</th>
            <th>Name</th>
            <th>Course</th>
            <th>Branch</th>
            <th>Actions</th>
        </tr>
    </thead>
    
    <tbody>
        <tr th:each="row, stat : ${records}" 
            th:style="${stat.odd} ? 'background-color: #f9f9f9' : 'background-color: white'">
            
            <td th:text="${stat.count}"></td>
            <td th:text="${row.rollno}"></td>
            <td th:text="${row.name}"></td>
            <td th:text="${row.course}"></td>
            <td th:text="${row.branch}"></td>
            
            <td>
                <a th:href="@{/edit/{id}(id=${row.rollno})}">Edit</a>
                <a th:href="@{/delete/{id}(id=${row.rollno})}">Delete</a>
            </td>
        </tr>
    </tbody>
</table>
```

---

## Tips & Tricks

### 1. Check List Size
```html
<div th:if="${records.size() > 0}">
    <p>Found <span th:text="${records.size()}"></span> records</p>
</div>
```

### 2. Conditional CSS Class
```html
<div th:classappend="${usertype == 'admin'} ? 'admin-panel' : 'user-panel'">
    Content here
</div>
```

### 3. Inline Thymeleaf Expression
```html
<p th:text="'Welcome ' + ${username} + '!'"></p>
```

### 4. Ternary Operator in Thymeleaf
```html
<p th:text="${user.age >= 18} ? 'Adult' : 'Minor'"></p>
```

### 5. Disable Fragment Parsing
```html
<div th:remove="all">
    <!-- This will be removed during rendering -->
</div>
```

---

## Common Issues & Solutions

| Issue | Cause | Solution |
|-------|-------|----------|
| Fragment not loading | Wrong fragment path | Use correct syntax: `~{templatename::fragmentname}` |
| CSS not applying | Wrong path in link tag | Use `th:href="@{/css/filename.css}"` |
| Image not showing | Incorrect image path | Place images in `static/images/` and use `th:src` |
| Loop not working | Collection is null | Check controller passes data correctly |
| Condition always false | Wrong variable name | Verify variable name matches model attribute |
| Fragment appears twice | Using both th:include and th:replace | Use only th:replace |

---

## File Structure Reference

```
src/main/resources/
├── templates/
│   ├── common.html         (Fragment definitions)
│   ├── home.html           (Uses fragments)
│   ├── about.html          (Uses fragments)
│   ├── contact.html        (Uses fragments)
│   └── register.html       (Uses fragments)
└── static/
    ├── css/
    │   └── pagestyle.css    (Stylesheet)
    └── images/
        ├── banner1.png
        ├── logo.png
        └── ...
```

---

## Best Practices Checklist

- ✅ Use fragments for reusable components (header, footer, menu)
- ✅ Keep CSS in separate files in static/css folder
- ✅ Place images in static/images folder
- ✅ Use meaningful fragment names
- ✅ Always use `th:` namespace in templates
- ✅ Check for null/empty collections before displaying
- ✅ Use ternary operators for simple conditions
- ✅ Use switch-case for multiple conditions
- ✅ Apply alternating row colors in tables
- ✅ Add meaningful alt text to images
- ✅ Cache CSS and images for better performance
- ✅ Use semantic HTML (thead, tbody, th, etc.)

---

## Summary

This guide covers essential Thymeleaf features:
- ✅ **Fragments** - Reusable template components
- ✅ **CSS Styling** - External stylesheet linking
- ✅ **Images** - Embedding images with th:src
- ✅ **Conditionals** - if/unless for conditional rendering
- ✅ **Switch-Case** - Multiple condition handling
- ✅ **Loops** - Iterating through collections with th:each
- ✅ **Best Practices** - DRY principle and code reusability
