# Paging in Spring Boot and MongoDB

## 1. What is Paging?

Paging is a technique used to show data in smaller chunks instead of loading all records at once.

This is very useful when:
- the database contains many records
- the UI should load faster
- users want to navigate through data page by page

For example, if a collection contains 100 student records, we may want to display 10 records per page.

---

## 2. Scenario

### Requirement
1. Insert 100 records into a MongoDB collection named `students`.
2. Create a REST API that shows records.
3. Use paging so that only a selected number of records are shown at a time.

---

## 3. Paging in Spring Boot with MongoDB

When using Spring Data with MongoDB, paging is supported using `Pageable`.

### Service method example

```java
public Page<students> showdatabypage(Pageable pageable)
{
    return sturepo.findAll(pageable);
}
```

### Explanation
- `Pageable` contains the page number and page size.
- `findAll(pageable)` returns a page of results from the repository.

---

## 4. Controller Example

```java
@GetMapping("/showdatabypage/{pageno}/{pagesize}")
public Page<students> showdatabypage(@PathVariable int pageno,
                                     @PathVariable int pagesize)
{
    Pageable pageable = PageRequest.of(pageno, pagesize);
    return stuserv.showdatabypage(pageable);
}
```

### Explanation
- `pageno` represents the current page number.
- `pagesize` represents how many records per page.
- `PageRequest.of(pageno, pagesize)` creates the paging request.

### Example URL
```text
http://localhost:8888/showdatabypage/0/10
```

This means:
- page number = 0
- show 10 records per page

---

## 5. Paging in Thymeleaf

Thymeleaf can also be used to create a paginated table with next and previous buttons.

### Example HTML

```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <style type="text/css">
        div { margin: 5px; padding: 5px; width: 600px; background-color: chartreuse; }
    </style>
</head>
<body>
    <div align="center">
        <table border="3" width="600" style="background-color: aquamarine; color: black;">
            <tr style="background-color: yellow; color: red;">
                <th colspan="4"><h2>Data by Pagination</h2></th>
            </tr>
            <tr style="background-color: crimson; color: white;">
                <th>Name</th>
                <th>Course</th>
                <th>Branch</th>
                <th>Admission Year</th>
            </tr>
            <tr th:each="student : ${studentspage}">
                <td th:text="${student.name}"></td>
                <td th:text="${student.course}"></td>
                <td th:text="${student.branch}"></td>
                <td th:text="${student.admissionYear}"></td>
            </tr>
        </table>
    </div>

    <div align="center">
        <form th:if="${studentspage.hasPrevious()}" th:action="@{/showpaging}" method="post" style="display:inline;">
            <input type="hidden" name="pageno" th:value="${currentpageno - 1}" />
            <input type="hidden" name="pagesize" th:value="${pagesize}" />
            <input type="submit" value="&laquo; Back" style="background-color: crimson; color: white; border: 1px solid black; padding: 5px 10px; cursor: pointer;" />
        </form>

        <span th:unless="${studentspage.hasPrevious()}"
              style="padding: 5px 10px; background-color: lightgray; color: darkgray; border: 1px solid gray; cursor: not-allowed;">
            &laquo; Back
        </span>

        <form th:if="${studentspage.hasNext()}" th:action="@{/showpaging}" method="post" style="display:inline;">
            <input type="hidden" name="pageno" th:value="${currentpageno + 1}" />
            <input type="hidden" name="pagesize" th:value="${pagesize}" />
            <input type="submit" value="Next &raquo;" style="background-color: crimson; color: white; border: 1px solid black; padding: 5px 10px; cursor: pointer;" />
        </form>

        <span th:unless="${studentspage.hasNext()}"
              style="padding: 5px 10px; background-color: lightgray; color: darkgray; border: 1px solid gray; cursor: not-allowed;">
            Next &raquo;
        </span>
    </div>
</body>
</html>
```

### Important points in this template
- `th:each` is used to loop through the page content.
- `studentspage.hasPrevious()` checks whether a previous page exists.
- `studentspage.hasNext()` checks whether a next page exists.
- Hidden fields send the current page number and page size to the controller.

---

## 6. Important Concepts

### Page number
This indicates which page the user is currently viewing.

### Page size
This indicates how many records should be shown per page.

### Example
- page number = 0
- page size = 10
- first page shows records 1 to 10

- page number = 1
- page size = 10
- second page shows records 11 to 20

---

## 7. Why Paging is Useful

Paging helps in:
- reducing server load
- improving application performance
- making UI easier to read
- avoiding long loading times

---

## 8. Summary

Paging is used to divide large result sets into smaller chunks of data.

In Spring Boot, it is typically implemented using:
- `Pageable`
- `PageRequest.of(pageno, pagesize)`
- repository methods such as `findAll(pageable)`

It can be used both for:
- REST APIs
- Thymeleaf-based web pages
