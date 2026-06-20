
# Entity Framework Core (EF Core)

Entity Framework Core (EF Core) is an Object-Relational Mapping (ORM) framework that acts as a bridge between your C# application and a relational database. 
It lets you work with database data as C# objects while automatically generating and executing the required SQL queries behind the scenes.

<img width="100%" alt="How ORM works with EF Core" src="https://github.com/user-attachments/assets/45bb068a-77b9-4ff0-ba15-088f060c7987" />

# What is Entity Framework Core?

**Entity Framework Core (EF Core)** is a software library (ORM) that allows your C# application to communicate with a database using **C# objects** instead of writing SQL queries manually.

Think of it as a **translator**.

```text
C# Language
      │
      ▼
Entity Framework Core
      │
      ▼
SQL Language
      │
      ▼
MySQL / SQL Server Database
```

EF Core understands both:

* C# Objects
* SQL Database

It translates between them automatically.

---

# Without EF Core

Suppose you want to save a contact.

You must write SQL yourself.

```sql
INSERT INTO contacts
(name, mobile_number)
VALUES
('John', '9876543210');
```

Your C# code has to execute this SQL manually.

```text
C#
   │
Write SQL
   │
Execute SQL
   │
Database
```

You are responsible for:

* Writing SQL
* Managing connections
* Mapping results
* Handling parameters

---

# With EF Core

Instead of SQL, you simply work with C# objects.

```csharp
Contact contact = new Contact
{
    Name = "John",
    MobileNumber = "9876543210"
};

_context.Contacts.Add(contact);
_context.SaveChanges();
```

That's all.

EF Core automatically generates:

```sql
INSERT INTO contacts
(name, mobile_number)
VALUES
('John', '9876543210');
```

You never wrote SQL.

---

# Think of EF Core as a Translator

Imagine two people speaking different languages.

```text
Developer
(Speaks C#)
       │
       ▼
Entity Framework Core
(Translator)
       │
       ▼
Database
(Speaks SQL)
```

Developer says

> Save this Contact.

EF Core tells MySQL

```sql
INSERT INTO contacts...
```

Database replies

> Contact saved.

EF Core tells your application

> Success!

---

# Why is it called ORM?

ORM stands for

**Object Relational Mapping**

Let's break it down.

## Object

Inside C#

```csharp
public class Contact
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

This is an **Object**.

---

## Relational

Inside MySQL

```text
contacts
-----------------------------
Id
Name
MobileNumber
```

This is a **Relational Table**.

---

## Mapping

EF Core connects both.

```text
C# Object                  Database Table

Contact       ─────────►   contacts

Id            ─────────►   id

Name          ─────────►   name

MobileNumber  ─────────►   mobile_number
```

This connection is called **Mapping**.

---

# Real Life Example

Imagine a bank employee.

You say:

> Deposit ₹5000.

You don't go inside the vault.

The employee does everything.

```text
You
 │
 │ "Deposit ₹5000"
 ▼
Bank Employee
 │
 │ Works with vault
 ▼
Bank Database
```

EF Core is exactly like the bank employee.

You simply say

```csharp
_context.Contacts.Add(contact);
```

EF Core handles everything behind the scenes.

---

# What happens internally?

```text
Step 1
Create Object

Contact
{
    Name = "John"
}

        │
        ▼

Step 2
EF Core reads the object

        │
        ▼

Step 3
Generates SQL

INSERT INTO contacts...

        │
        ▼

Step 4
Executes SQL

        │
        ▼

Step 5
Database stores the data

        │
        ▼

Step 6
Returns Success
```

---

# Reading Data

Without EF Core

```sql
SELECT *
FROM contacts;
```

Then you manually convert every row into a C# object.

---

With EF Core

Simply write

```csharp
var contacts = _context.Contacts.ToList();
```

EF Core automatically executes

```sql
SELECT *
FROM contacts;
```

and converts the rows into

```csharp
List<Contact>
```

---

# Updating Data

You simply modify the object.

```csharp
contact.Name = "Peter";

_context.SaveChanges();
```

EF Core generates

```sql
UPDATE contacts
SET Name='Peter'
WHERE Id=1;
```

---

# Deleting Data

```csharp
_context.Remove(contact);

_context.SaveChanges();
```

EF Core generates

```sql
DELETE FROM contacts
WHERE Id=1;
```

---

# In Simple Words

```text
You write C# Code
        │
        ▼
Entity Framework Core
        │
Converts C# to SQL
        │
        ▼
Database Executes SQL
        │
        ▼
Result Returned as C# Objects
```

---

# One-Line Definition

> **Entity Framework Core (EF Core)** is an **Object-Relational Mapping (ORM)** framework that acts as a bridge between your C# application and a relational database. It lets you work with database data as C# objects while automatically generating and executing the required SQL queries behind the scenes.


**ORM (Object-Relational Mapping)** is a technique that maps objects in your application to tables in a relational database. It lets you work with database records as objects instead of writing raw SQL for every operation.

| Technology                          | Purpose                                                                                                                                                                    |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ASP.NET Core Web API**            | Backend framework used to build REST APIs. It receives HTTP requests from the frontend, processes business logic, interacts with the database, and returns JSON responses. |
| **Entity Framework Core (EF Core)** | ORM (Object-Relational Mapping) framework that allows developers to work with database tables using C# classes and LINQ instead of writing raw SQL queries.                |


### How they work together

```text
React JS
    │
HTTP Request (GET, POST, PUT, DELETE)
    │
ASP.NET Core Web API
    │
Business Logic
    │
Entity Framework Core (ORM)
    │
MySQL Database
```

### Example

#### Contact Entity (C#)

```csharp
public class Contact
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

#### Save Data using EF Core

```csharp
_context.Contacts.Add(contact);
await _context.SaveChangesAsync();
```

EF Core automatically generates SQL similar to:

```sql
INSERT INTO contacts (name, mobile_number)
VALUES ('John', '9876543210');
```

Without EF Core, you would have to write the SQL manually.

### Responsibilities

| Layer                 | Responsibility                                     |
| --------------------- | -------------------------------------------------- |
| React JS              | User Interface (Frontend)                          |
| ASP.NET Core Web API  | API endpoints, validation, business logic          |
| Entity Framework Core | Database access, CRUD operations, query generation |
| MySQL                 | Stores application data                            |

### In Short

* **ASP.NET Core Web API** → Builds the backend API.
* **Entity Framework Core** → Connects the backend to the database using C# objects.
* **MySQL** → Stores the actual data.
