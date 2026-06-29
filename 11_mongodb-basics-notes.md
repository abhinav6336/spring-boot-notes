# MongoDB Basics Notes

## 1. What is MongoDB?

MongoDB is a database system used to store data in a flexible and scalable way.

### Key points
- It is a NoSQL database.
- It is schema-less, which means documents in a collection do not need the same structure.
- It is generally faster than traditional relational databases for many modern applications.

### MongoDB vs Relational Database

| Relational Database | MongoDB |
|---|---|
| Table | Collection |
| Row | Document |
| Column/Field | Field |

---

## 2. Tools Required

To work with MongoDB, you usually need:

1. MongoDB Compass - graphical interface (GUI)
2. MongoDB Shell - command-line interface (CLI)

---

## 3. Basic MongoDB Commands

### Show all databases
```bash
show dbs
```

### Use or create a database
```bash
use databasename
```

If the database already exists, it opens it. If not, MongoDB creates it when data is inserted.

### Create a collection
```bash
db.createCollection("collectionname")
```

Example:
```bash
db.createCollection("students")
```

### Show collections in the current database
```bash
show tables
```

---

## 4. Insert Data into MongoDB

### Insert one document
```bash
db.collectionname.insertOne({ fieldname: value, ... })
```

Example:
```bash
db.students.insertOne({ rollno: 101, name: 'Dhananjay', course: 'Phd', branch: 'CS' })
```

### Insert multiple documents
```bash
db.collectionname.insertMany([{ json1 }, { json2 }])
```

Example:
```bash
db.students.insertMany([
  { rollno: 102, name: 'Stuti', course: 'B.Tech.', branch: 'AIML' },
  { rollno: 103, name: 'Manavi', course: 'M.Tech.', branch: 'AIML' },
  { rollno: 104, name: 'Sandhya', course: 'B.Tech.', branch: 'CS' },
  { rollno: 105, name: 'Hema', course: 'B.Tech.', branch: 'IT' },
  { rollno: 106, name: 'Komal', course: 'MCA.', branch: 'CS' }
])
```

---

## 5. Read Data

### Show all documents
```bash
db.collectionname.find()
```

Example:
```bash
db.students.find()
```

---

## 6. Query Operators in MongoDB

### Comparison Operators

#### $eq - equal to
```bash
db.collectionname.find({ fieldname: { $eq: value } })
```

Example:
```bash
db.students.find({ branch: { $eq: 'CS' } })
```

#### $gt - greater than
```bash
db.students.find({ rollno: { $gt: 103 } })
```

#### $gte - greater than or equal to
```bash
db.students.find({ rollno: { $gte: 103 } })
```

#### $lt - less than
```bash
db.students.find({ rollno: { $lt: 103 } })
```

#### $lte - less than or equal to
```bash
db.students.find({ rollno: { $lte: 103 } })
```

#### $ne - not equal to
```bash
db.students.find({ branch: { $ne: 'AIML' } })
```

### Logical Operators

#### $and
```bash
db.collectionname.find({ $and: [{ condition1 }, { condition2 }] })
```

Example:
```bash
db.students.find({ $and: [{ course: { $eq: 'B.Tech.' } }, { branch: { $eq: 'CS' } }] })
```

#### $or
```bash
db.students.find({ $or: [{ course: { $eq: 'BCA' } }, { course: { $eq: 'MCA' } }] })
```

#### $not
```bash
db.students.find({ course: { $not: { $eq: 'AIML' } } })
```

### Element Operators

#### $exists
```bash
db.students.find({ fees: { $exists: true } })
```

Example of inserting a document with a field:
```bash
db.students.insertOne({ rollno: 111, name: 'Ajay', course: 'B.Tech.', branch: 'CS', fees: 120000 })
```

### Array Operators

#### $in
```bash
db.collectionname.find({ fieldname: { $in: [value1, value2] } })
```

Example:
```bash
db.students.find({ course: { $in: ['BCA', 'MCA'] } })
```

#### $all
```bash
db.collectionname.find({ tags: { $all: ['Java', 'MongoDB'] } })
```

#### $nin
```bash
db.collectionname.find({ course: { $nin: ['BCA', 'MCA'] } })
```

---

## 7. Delete Records

### Delete one document
```bash
db.collectionname.deleteOne(criteria)
```

Example:
```bash
db.students.deleteOne({ name: { $eq: 'Komal' } })
```

### Delete many documents
```bash
db.collectionname.deleteMany(criteria)
```

Example:
```bash
db.students.deleteMany({ course: { $eq: 'B.Tech' } })
```

---

## 8. Update Records

### Update one document
```bash
db.collectionname.updateOne({ condition }, { $set: { value } })
```

Example:
```bash
db.students.updateOne({ rollno: 101 }, { $set: { name: 'Anuj Kapoor' } })
```

### Update many documents
```bash
db.collectionname.updateMany({ condition }, { $set: { field: value } })
```

---

## 9. Drop Operations

### Drop a collection
```bash
db.collectionname.drop()
```

### Drop a database
```bash
db.dropDatabase()
```

---

## 10. Assignment

### Task
Create a database named `training2026` and perform the following operations.

#### 1. Create a database
```bash
use training2026
```

#### 2. Create a collection named `emp`
```bash
db.createCollection("emp")
```

#### 3. Insert 10 employee records
Each record should include:
- `eid`
- `ename`
- `edesig`
- `esal`
- `edept`

`edept` should contain only values like `10`, `20`, or `30`.

#### 4. Queries to perform

##### a. Increase salary by 1000 for each employee
```bash
db.emp.updateMany({}, { $inc: { esal: 1000 } })
```

##### b. Show employees whose salary is greater than 10000
```bash
db.emp.find({ esal: { $gt: 10000 } })
```

##### c. Show employees whose department is either 10 or 30
```bash
db.emp.find({ edept: { $in: [10, 30] } })
```

---

## 11. Important Notes

- MongoDB stores data as JSON-like documents.
- Each document can have a different structure.
- It is useful for applications that need flexible and fast data handling.
- For large-scale applications, MongoDB is commonly used with distributed systems.
