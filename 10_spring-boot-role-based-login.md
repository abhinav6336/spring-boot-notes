# Spring Boot Role-Based Login Example

## Objective

Create a simple Spring Boot application where users can log in using a username and password, and then be redirected to different pages based on their role:

- admin → admin page
- manager → manager page
- staff → staff page

This example uses Thymeleaf for the UI and a database table named `usrs`.

---

## 1. Database Table

Create a table named `usrs` with the following fields:

- `uname` → username
- `passwd` → password
- `role` → user role

### SQL

```sql
create table usrs(
    uname varchar(40) primary key,
    passwd varchar(40) not null,
    role varchar(30) not null
);
```

### Insert sample data

```sql
insert into usrs values('admin', 'admin', 'admin');
insert into usrs values('manager', 'manager', 'manager');
insert into usrs values('staff', 'staff', 'staff');
```

---

## 2. Login Form Design

The login form contains:

- User Name field
- Password field
- Login button

### Example form idea

- User Name: text field
- Password: password field
- Button: Login

---

## 3. Spring Boot Flow

The application flow is:

1. Open the login page.
2. Submit the username and password.
3. Check the user in the database.
4. If the password is correct, redirect based on the role.

---

## 4. Entity Class

Create an entity class for the table.

```java
package com.training.Entity;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name = "usrs")
public class usrs {

    @Id
    private String uname;
    private String passwd;
    private String role;

    public String getUname() {
        return uname;
    }

    public void setUname(String uname) {
        this.uname = uname;
    }

    public String getPasswd() {
        return passwd;
    }

    public void setPasswd(String passwd) {
        this.passwd = passwd;
    }

    public String getRole() {
        return role;
    }

    public void setRole(String role) {
        this.role = role;
    }
}
```

> Note: In real applications, passwords should be encrypted or hashed before storing them.

---

## 5. Repository Interface

```java
package com.training.Repository;

import java.util.Optional;

import org.springframework.data.jpa.repository.JpaRepository;

import com.training.Entity.usrs;

public interface UsrsRepository extends JpaRepository<usrs, String> {
    Optional<usrs> findByUname(String uname);
}
```

---

## 6. Service Layer

```java
package com.training.Service;

import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.training.Entity.usrs;
import com.training.Repository.UsrsRepository;

@Service
public class UsrsService {

    @Autowired
    UsrsRepository repo;

    public Optional<usrs> searchdata(String uname) {
        return repo.findByUname(uname);
    }
}
```

---

## 7. Controller

```java
package com.training.Controller;

import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

import com.training.Entity.usrs;
import com.training.Service.UsrsService;

@Controller
public class AppController {

    @Autowired
    UsrsService usrsserv;

    @GetMapping("/")
    public String index() {
        return "frmlogin";
    }

    @PostMapping("/loginres")
    public String loginres(@RequestParam String txtun, @RequestParam String txtpw) {
        Optional<usrs> records = usrsserv.searchdata(txtun);

        if (records.isPresent()) {
            usrs usr = records.get();

            if (usr.getPasswd().equals(txtpw) && usr.getRole().equals("admin")) {
                return "admin/admin_home";
            } else if (usr.getPasswd().equals(txtpw) && usr.getRole().equals("manager")) {
                return "manager/manager_home";
            } else if (usr.getPasswd().equals(txtpw) && usr.getRole().equals("staff")) {
                return "staff/staff_home";
            }
        }

        return "frmlogin";
    }
}
```

### Important note

- The return value should be a view name such as `admin/admin_home`.
- If you use `return "/admin/admin_home";`, it may not behave as expected in some Thymeleaf setups.

---

## 8. Login Page Template

### `frmlogin.html`

```html
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="~{fragments/common :: bootstrap_links}"></head>
<body>
    <div class="container">
        <div class="row">
            <div class="col-md-3"></div>
            <div class="col-md-6">
                <form method="post" th:action="@{/loginres}">
                    User Name
                    <input type="text" name="txtun" class="form-control" /><br/>

                    Password
                    <input type="password" name="txtpw" class="form-control" /><br/>

                    <input type="submit" value="Login" name="b1" class="btn btn-primary" />
                </form>
            </div>
            <div class="col-md-3"></div>
        </div>
    </div>
</body>
</html>
```

---

## 9. Role Pages

### Admin page

```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <link rel="stylesheet" th:href="@{/css/pagestyle.css}" type="text/css" />
</head>
<body>
    <h1 class="header">Admin Home</h1>
</body>
</html>
```

### Manager page

```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <link rel="stylesheet" th:href="@{/css/pagestyle.css}" type="text/css" />
</head>
<body>
    <h1 class="header">Manager Home</h1>
</body>
</html>
```

### Staff page

```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <link rel="stylesheet" th:href="@{/css/pagestyle.css}" type="text/css" />
</head>
<body>
    <h1 class="header">Staff Home</h1>
</body>
</html>
```

---

## 10. CSS Styling

Place the CSS file in the `static/css` folder.

### `static/css/pagestyle.css`

```css
.header {
    font-family: Impact;
    color: maroon;
    font-size: 35px;
    text-align: center;
}
```

You can also use inline styles or a `<style>` tag, but external CSS is a better practice.

---

## 11. Reusable Bootstrap Fragment

Create a common Thymeleaf fragment for Bootstrap.

### `templates/fragments/common.html`

```html
<html xmlns:th="http://www.thymeleaf.org">
<head th:fragment="bootstrap_links">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/js/bootstrap.bundle.min.js"></script>
</head>
</html>
```

---

## 12. Important Points

- Use `@Controller` for page-based views.
- Use `@GetMapping("/")` for the login page.
- Use `@PostMapping("/loginres")` for form submission.
- Use `@RequestParam` to receive form values.
- Use Thymeleaf expressions like `th:action` and `th:href` for proper URL handling.
- Always keep security in mind; do not use plain text passwords in real projects.

---

## 13. Summary

This example demonstrates:

- database connection with Spring Boot
- entity and repository creation
- login validation
- role-based redirection
- Thymeleaf templates
- CSS and Bootstrap usage
