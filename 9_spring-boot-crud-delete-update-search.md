# Spring Boot CRUD Operations: Delete, Update & Search

## Overview
This guide covers implementing Delete, Update, and Search operations in a Spring Boot application with Thymeleaf. These are advanced CRUD operations that enhance your application's functionality.

---

## Part 1: Record Deletion

### 1.1 Service Layer

```java
package com.training.Service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.training.Entity.Student;
import com.training.Repository.StudentRepository;

@Service
public class StudentService {
    
    @Autowired
    private StudentRepository sturepo;
    
    // Delete record by ID
    public void deletedata(int rollno) {
        sturepo.deleteById(rollno);
    }
    
    // Get all students (for refreshing list after deletion)
    public List<Student> showall() {
        return sturepo.findAll();
    }
}
```

**Tips:**
- Always verify record exists before deletion (use `existsById()`)
- Consider soft delete (mark as deleted instead of physical deletion) for critical data
- Add logging to track deletions for audit purposes

### 1.2 Controller Layer

```java
package com.training.Controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

import com.training.Service.StudentService;

@Controller
public class AppController {
    
    @Autowired
    private StudentService stuserv;
    
    // Delete API endpoint
    @GetMapping("/deleteapi/{rollno}")
    public String deleteapi(Model model, @PathVariable int rollno) {
        // Check if record exists before deletion
        if (stuserv.existsById(rollno)) {
            stuserv.deletedata(rollno);
            model.addAttribute("message", "Record deleted successfully!");
        } else {
            model.addAttribute("error", "Record not found!");
        }
        
        // Refresh the list
        model.addAttribute("records", stuserv.showall());
        return "showrecords";
    }
}
```

**Best Practices:**
- Check record existence before deletion
- Add success/error messages for user feedback
- Return refreshed list after deletion
- Use meaningful status messages

### 1.3 Thymeleaf Template (showrecords.html)

```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Student Records</title>
</head>
<body>
    <h1>Students Records</h1>
    
    <!-- Success/Error Messages -->
    <div th:if="${message}" style="color: green; padding: 10px; border: 1px solid green;">
        <span th:text="${message}"></span>
    </div>
    <div th:if="${error}" style="color: red; padding: 10px; border: 1px solid red;">
        <span th:text="${error}"></span>
    </div>
    
    <table width="600px" border="2">
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
                <!-- Delete Button with Confirmation -->
                <a href="#" th:onclick="'return confirm(\'Are you sure you want to delete this record?\')'
                   th:href="@{/deleteapi/{rollno}(rollno=${row.rollno})}">
                    Delete
                </a>
            </td>
        </tr>
    </table>
</body>
</html>
```

**Thymeleaf Tips:**
- Use `th:onclick` with confirmation dialog to prevent accidental deletion
- `@{/deleteapi/{rollno}(rollno=${row.rollno})}` generates URL with parameter
- Display success/error messages after operation

**Deletion Workflow:**
1. User clicks Delete link
2. JavaScript confirmation dialog appears
3. If confirmed, request sent to `/deleteapi/{rollno}`
4. Controller deletes record and refreshes list
5. Success message displayed to user

---

## Part 2: Record Update

### 2.1 Service Layer

```java
@Service
public class StudentService {
    
    @Autowired
    private StudentRepository sturepo;
    
    // Retrieve student by ID for updating
    public Optional<Student> searchdata(int rollno) {
        return sturepo.findById(rollno);
    }
    
    // Update student record
    public Student updateStudentRecord(Student student) {
        return sturepo.save(student);
    }
    
    // Get all students
    public List<Student> showall() {
        return sturepo.findAll();
    }
}
```

**Tips:**
- `Optional` handles cases where record doesn't exist
- `save()` method works for both insert and update
- Always validate data before saving

### 2.2 Controller Layer

```java
@Controller
public class AppController {
    
    @Autowired
    private StudentService stuserv;
    
    // Show update form (GET request)
    @GetMapping("/updatereq/{rollno}")
    public String updatereq(Model model, @PathVariable int rollno) {
        // Fetch record from database
        Optional<Student> obj = stuserv.searchdata(rollno);
        
        // Check if record exists
        if (obj.isPresent()) {
            Student s = obj.get();
            model.addAttribute("rollno", s.getRollno());
            model.addAttribute("name", s.getName());
            model.addAttribute("course", s.getCourse());
            model.addAttribute("branch", s.getBranch());
            return "frmupdate";
        } else {
            model.addAttribute("error", "Record not found!");
            return "showrecords";
        }
    }
    
    // Process update (POST request)
    @PostMapping("/updateres")
    public String updateres(Model model,
                           @RequestParam int rollno,
                           @RequestParam String name,
                           @RequestParam String course,
                           @RequestParam String branch) {
        try {
            // Create updated student object
            Student student = new Student(rollno, name, course, branch);
            stuserv.updateStudentRecord(student);
            
            model.addAttribute("message", "Record updated successfully!");
            model.addAttribute("records", stuserv.showall());
            return "showrecords";
        } catch (Exception e) {
            model.addAttribute("error", "Failed to update record: " + e.getMessage());
            return "frmupdate";
        }
    }
}
```

**Best Practices:**
- Validate record existence before showing update form
- Use `@RequestParam` for form data binding
- Add try-catch for error handling
- Display success/error messages

### 2.3 Thymeleaf Template (frmupdate.html)

```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Update Student Record</title>
    <style>
        body { font-family: Arial; margin: 20px; }
        h1 { color: #333; }
        form { width: 400px; }
        table { width: 100%; }
        td { padding: 8px; }
        input { width: 100%; padding: 6px; box-sizing: border-box; }
        button { padding: 8px 20px; background-color: #4CAF50; color: white; border: none; cursor: pointer; }
        button:hover { background-color: #45a049; }
    </style>
</head>
<body>
    <h1>Update Student Record</h1>
    
    <form method="post" th:action="@{/updateres}">
        <table>
            <!-- Roll Number (Read-only) -->
            <tr>
                <td><label>Roll Number</label></td>
                <td>
                    <input type="text" name="rollno" th:value="${rollno}" readonly />
                </td>
            </tr>
            
            <!-- Name -->
            <tr>
                <td><label>Name</label></td>
                <td>
                    <input type="text" name="name" th:value="${name}" required />
                </td>
            </tr>
            
            <!-- Course -->
            <tr>
                <td><label>Course</label></td>
                <td>
                    <input type="text" name="course" th:value="${course}" required />
                </td>
            </tr>
            
            <!-- Branch -->
            <tr>
                <td><label>Branch</label></td>
                <td>
                    <input type="text" name="branch" th:value="${branch}" required />
                </td>
            </tr>
            
            <!-- Buttons -->
            <tr>
                <td colspan="2" style="text-align: center; padding-top: 20px;">
                    <button type="submit">Update</button>
                    <a href="/showrecords" style="margin-left: 10px; padding: 8px 20px; background-color: #ccc; color: #333; text-decoration: none; border-radius: 4px;">Cancel</a>
                </td>
            </tr>
        </table>
    </form>
</body>
</html>
```

**Form Tips:**
- Roll Number field is `readonly` to prevent ID changes
- Use `th:value="${fieldname}"` to pre-populate form fields
- Add `required` attribute for mandatory fields
- Provide Cancel button to go back

**Update Workflow:**
1. User clicks Edit on a record
2. GET request to `/updatereq/{rollno}` fetches record data
3. Form loads with pre-populated values
4. User modifies fields and clicks Update
5. POST request to `/updateres` processes the update
6. Success message displayed and list refreshed

---

## Part 3: Search/Filter Records

### 3.1 Service Layer

```java
@Service
public class StudentService {
    
    @Autowired
    private StudentRepository sturepo;
    
    // Search student by roll number
    public Optional<Student> searchdata(int rollno) {
        return sturepo.findById(rollno);
    }
    
    // Search by name (advanced)
    public List<Student> searchByName(String name) {
        return sturepo.findByNameContaining(name);
    }
    
    // Get all students
    public List<Student> showall() {
        return sturepo.findAll();
    }
}
```

**Tips:**
- Use `findById()` for searching by primary key
- Use `findByNameContaining()` for partial matches
- Add custom query methods for complex searches

### 3.2 Repository Enhancement

```java
package com.training.Repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
import com.training.Entity.Student;
import java.util.List;

@Repository
public interface StudentRepository extends JpaRepository<Student, Integer> {
    
    // Custom search methods
    List<Student> findByNameContaining(String name);
    List<Student> findByCourse(String course);
    List<Student> findByBranch(String branch);
}
```

### 3.3 Controller Layer

```java
@Controller
public class AppController {
    
    @Autowired
    private StudentService stuserv;
    
    // Search API - GET request with parameter
    @GetMapping("/searchapi")
    public String searchapi(Model model, @RequestParam int rollno) {
        try {
            // Search for record
            Optional<Student> obj = stuserv.searchdata(rollno);
            
            if (obj.isPresent()) {
                // Create list with single result
                List<Student> records = new ArrayList<>();
                records.add(obj.get());
                model.addAttribute("records", records);
                model.addAttribute("message", "Record found!");
            } else {
                model.addAttribute("error", "Record not found!");
                model.addAttribute("records", new ArrayList<>());
            }
            
            return "showrecords";
        } catch (Exception e) {
            model.addAttribute("error", "Search failed: " + e.getMessage());
            model.addAttribute("records", new ArrayList<>());
            return "showrecords";
        }
    }
    
    // Search by name (advanced)
    @GetMapping("/searchbyname")
    public String searchByName(Model model, @RequestParam String name) {
        try {
            List<Student> records = stuserv.searchByName(name);
            
            if (records.isEmpty()) {
                model.addAttribute("error", "No records found for: " + name);
            } else {
                model.addAttribute("message", "Found " + records.size() + " record(s)");
            }
            
            model.addAttribute("records", records);
            return "showrecords";
        } catch (Exception e) {
            model.addAttribute("error", "Search failed: " + e.getMessage());
            model.addAttribute("records", new ArrayList<>());
            return "showrecords";
        }
    }
}
```

**Best Practices:**
- Check if result exists before adding to list
- Handle empty results gracefully
- Add error messages for failed searches
- Always return a valid list (even if empty)

### 3.4 Thymeleaf Search Form (showrecords.html)

```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Student Records</title>
</head>
<body>
    <h1>Students Records</h1>
    
    <!-- Search Section -->
    <div style="margin-bottom: 20px; padding: 10px; background-color: #f5f5f5; border-radius: 4px;">
        <h3>Search Student</h3>
        
        <!-- Search by Roll Number -->
        <form method="get" action="/searchapi" style="display: inline; margin-right: 30px;">
            <label>By Roll Number:</label>
            <input type="number" name="rollno" placeholder="Enter Roll Number" required />
            <button type="submit">Search</button>
        </form>
        
        <!-- Search by Name -->
        <form method="get" action="/searchbyname" style="display: inline;">
            <label>By Name:</label>
            <input type="text" name="name" placeholder="Enter Student Name" required />
            <button type="submit">Search</button>
        </form>
        
        <!-- Reset/Show All -->
        <a href="/showrecords" style="margin-left: 20px; padding: 6px 12px; background-color: #999; color: white; text-decoration: none; border-radius: 4px;">Show All</a>
    </div>
    
    <!-- Messages -->
    <div th:if="${message}" style="color: green; padding: 10px; border: 1px solid green; margin-bottom: 10px;">
        <span th:text="${message}"></span>
    </div>
    <div th:if="${error}" style="color: red; padding: 10px; border: 1px solid red; margin-bottom: 10px;">
        <span th:text="${error}"></span>
    </div>
    
    <!-- Records Table -->
    <table width="600px" border="2">
        <tr style="background-color: black; color: white;">
            <td>Roll Number</td>
            <td>Name</td>
            <td>Course</td>
            <td>Branch</td>
            <td>Actions</td>
        </tr>
        
        <tr th:if="${records.isEmpty()}">
            <td colspan="5" style="text-align: center; color: #999;">
                No records found
            </td>
        </tr>
        
        <tr th:each="row:${records}">
            <td th:text="${row.rollno}"></td>
            <td th:text="${row.name}"></td>
            <td th:text="${row.course}"></td>
            <td th:text="${row.branch}"></td>
            <td>
                <a th:href="@{/updatereq/{rollno}(rollno=${row.rollno})}">Edit</a>
                <a th:href="@{/deleteapi/{rollno}(rollno=${row.rollno})}" 
                   onclick="return confirm('Delete this record?')">Delete</a>
            </td>
        </tr>
    </table>
</body>
</html>
```

**Search Features:**
- Search by Roll Number (exact match)
- Search by Name (partial match)
- Show All link to reset search
- Display message with result count
- Handle empty results gracefully

---

## Complete Workflow Summary

### Delete Operation Flow
```
User clicks Delete → Confirmation Dialog → DELETE request → Controller → Service → Repository → 
Database Update → Refresh List → Success Message → Display updated records
```

### Update Operation Flow
```
User clicks Edit → GET request → Controller retrieves data → Form loads with data → 
User modifies fields → POST request → Controller validates → Service saves → 
Success Message → Display updated records
```

### Search Operation Flow
```
User enters search criteria → Submit form → GET request with parameter → Controller searches → 
Service queries database → Results returned → Display matching records
```

---

## Important Tips & Tricks

### 1. Prevent Accidental Deletion
```html
<a th:href="@{/deleteapi/{id}(id=${row.rollno})}" 
   onclick="return confirm('Are you absolutely sure?')">Delete</a>
```

### 2. Handle Null/Empty Records
```java
if (obj.isPresent()) {
    Student s = obj.get();
    // Process record
} else {
    model.addAttribute("error", "Record not found!");
}
```

### 3. Add Logging for Audit Trail
```java
@GetMapping("/deleteapi/{rollno}")
public String deleteapi(@PathVariable int rollno) {
    logger.info("Deleting record: " + rollno);
    stuserv.deletedata(rollno);
    logger.info("Record deleted successfully");
    return "redirect:/showrecords";
}
```

### 4. Case-Insensitive Search
```java
// In Repository
@Query("SELECT s FROM Student s WHERE LOWER(s.name) LIKE LOWER(CONCAT('%', :name, '%'))")
List<Student> searchByNameIgnoreCase(@Param("name") String name);
```

### 5. Validate Data Before Update
```java
if (name == null || name.trim().isEmpty()) {
    model.addAttribute("error", "Name cannot be empty!");
    return "frmupdate";
}
```

---

## Common Issues & Solutions

| Issue | Cause | Solution |
|-------|-------|----------|
| Record not found after deletion | Timing issue | Use transactions |
| Form not pre-populated | Incorrect field name | Check `th:value` binding |
| Search returns empty | Wrong parameter | Verify parameter name in form |
| Delete doesn't work | Missing confirmation | Add JavaScript confirmation |
| Duplicate records in search | No limit applied | Add pagination |

---

## Testing the Operations

### Test Delete
1. Open `/showrecords`
2. Click Delete on any record
3. Confirm deletion in dialog
4. Verify record is removed

### Test Update
1. Click Edit on a record
2. Modify any field
3. Click Update
4. Verify changes are saved

### Test Search
1. Enter Roll Number and click Search
2. Verify single result displays
3. Enter Name and click Search
4. Verify all matching records display

---

## Summary

This guide covers three essential CRUD operations:
- ✅ **Delete** - Remove records with confirmation
- ✅ **Update** - Modify existing records with pre-populated forms
- ✅ **Search** - Find records by Roll Number or Name
- ✅ **Error Handling** - Graceful error messages
- ✅ **User Feedback** - Success/error notifications
- ✅ **Data Validation** - Check existence before operations
