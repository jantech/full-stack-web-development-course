# Warranty Tracker API - .NET 10 Web API Implementation Guide

## Architecture

### Beginner-Friendly
<img width="65%" alt="simplified-software-architecture-flow-diagram" src="https://github.com/user-attachments/assets/bc37809c-894f-4845-bfc0-4c6961971f50" />

```text
Client
  ↓
Controller
  ↓
DbContext
  ↓
MySQL Database
```

### Layer Responsibilities

#### Controller

* Receive HTTP Requests
* Validate incoming DTOs
* Call Service methods
* Return HTTP responses

#### Service

* Business Logic
* Validation Rules
* DTO ↔ Entity Mapping
* Transaction Coordination

#### Repository

* Database Access
* EF Core Queries
* CRUD Operations
* Filtering and Includes

#### DbContext

* Entity Configuration
* Relationships
* Database Connection

---

# Step 1 - Create Project

Create a new .NET 8 Web API project.

<img width="90%" alt="image" src="https://github.com/user-attachments/assets/6fcd20e6-4cfa-4976-af5b-904e5dafaf01" />

<img width="90%" alt="image" src="https://github.com/user-attachments/assets/06c185a1-f8eb-4d1b-823a-3081917749c5" />

<img width="90%" alt="image" src="https://github.com/user-attachments/assets/987edc4e-8168-4c3f-a64f-d366a4007eb1" />

<img width="90%" alt="image" src="https://github.com/user-attachments/assets/c1c04a74-1cc3-46ef-9f4a-cab20107cc5a" />

Remove sample WeatherForecast files if not required.

---

# Step 2 - Install Required Packages

```bash
dotnet add package Microsoft.EntityFrameworkCore

dotnet add package Microsoft.EntityFrameworkCore.Design

dotnet add package Pomelo.EntityFrameworkCore.MySql

dotnet add package Scalar.AspNetCore

dotnet add package Scalar.AspNetCore.Microsoft
```

---

## How to add nuget packages 

<img width="70%" alt="image" src="https://github.com/user-attachments/assets/59b05b22-437c-46e1-996e-2fb064d48e95" />
<img width="90%" alt="image" src="https://github.com/user-attachments/assets/0dba58c7-78ef-4b11-accc-5902513f8540" />

#### Add in Program.cs:

```csharp
builder.Services.AddOpenApi();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
    app.MapScalarApiReference(options =>
    {
        options
            .WithTitle("Warranty Tracker API")
            .WithTheme(ScalarTheme.Purple)
            .WithDefaultHttpClient(ScalarTarget.CSharp, ScalarClient.HttpClient);
    });
}

app.Run();
```

#### Add in Properties/launchSettings.json

Verify below in Launch Settings file 

✅ Environment set to Development

✅ launchBrowser set to true

**✅ launchUrl set to scalar/v1**


#### Example:

<img width="786" height="623" alt="image" src="https://github.com/user-attachments/assets/070bea99-23ce-43e6-8a21-e5eee6f99b0d" />


### To view your Scalar UI:

1.	Press **F5** in Visual Studio
2.	Browser will automatically open to: **https://localhost:7045/scalar/v1**
3.	You'll see your interactive API documentation with the WeatherForecast endpoint

**Manual URLs (if needed):**

HTTPS: https://localhost:7045/scalar/v1

HTTP: http://localhost:5062/scalar/v1

<img width="80%" alt="image" src="https://github.com/user-attachments/assets/a82dfe7a-f1d2-4cf4-8b04-03dd45dbf077" />

---

# Step 3 - Database Design

## Database Schema

See [Warranty Tracker Database Schema](./warranty-tracker-db-schema.md)

---

# Step 4 - Recommended Folder Structure

```text
WarrantyTracker.Api
│
├── Controllers
│   ├── BrandsController.cs
│   ├── DevicesController.cs
│   └── WarrantiesController.cs
│
├── Models
│   ├── Brand.cs
│   ├── Device.cs
│   └── WarrantyRegistration.cs
│
├── DTOs
│   ├── BrandDto.cs
│   ├── CreateBrandDto.cs
│   ├── UpdateBrandDto.cs
│   ├── DeviceDto.cs
│   ├── CreateDeviceDto.cs
│   └── UpdateDeviceDto.cs
│
├── Data
│   └── ApplicationDbContext.cs
│
├── Helpers
│   └── WarrantyHelper.cs
│
├── Program.cs
│
├── appsettings.json
│
└── appsettings.Development.json
```

---

# Configure Database Connection


## Step 1: Store Database Details

### appsettings.json

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "server=localhost;database=warranty_tracker;user=root;password=root123;pooling=true;charset=utf8;convert zero datetime=true;"
  }
}
```

This tells the application:

```text
MySQL Server = localhost
Database = warranty_tracker
Username = root
Password = root123
```

---

## Step 2: Connect ASP.NET to MySQL

### Program.cs

```csharp
var connectionString =
    builder.Configuration.GetConnectionString("DefaultConnection");

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseMySql(
        connectionString,
        ServerVersion.AutoDetect(connectionString)
    ));
```

explanation:

```text
Read connection string
        ↓
Connect to MySQL
        ↓
Create AppDbContext
```

---

## Step 3: Create AppDbContext

### AppDbContext.cs

```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    public DbSet<Brand> Brands { get; set; }

    public DbSet<Device> Devices { get; set; }
}
```

explanation:

```text
AppDbContext
    ↓
Brands Table
    ↓
Devices Table
```

---

## Step 4: Create and Use in Controller

<img width="70%" alt="image" src="https://github.com/user-attachments/assets/ade397be-6f0d-4f1c-a7d8-b937c14b8c64" />

<img width="70%" alt="image" src="https://github.com/user-attachments/assets/621c84fb-363d-45e2-807c-306cfff14cba" />

<img width="70%" alt="image" src="https://github.com/user-attachments/assets/ffa777c8-98a3-466b-a0e5-df0171a90fec" />

<img width="70%" alt="image" src="https://github.com/user-attachments/assets/681e133e-6897-4084-8b36-ce5b8e6d3b63" />


```csharp
public class BrandsController : ControllerBase
{
    private readonly AppDbContext _context;

    public BrandsController(AppDbContext context)
    {
        _context = context;
    }
}
```

Now the controller can access database tables.

---

## Complete Flow

```text
Browser
   ↓
BrandsController
   ↓
AppDbContext
   ↓
Brands Table
   ↓
MySQL Database
```

Example:

```csharp
var brands = await _context.Brands.ToListAsync();
```

EF Core automatically converts it into:

```sql
SELECT * FROM brands;
```

---

### One-line Explanation

**AppDbContext is the bridge between ASP.NET Core and MySQL.**

```text
ASP.NET Core
      ↕
  AppDbContext
      ↕
     MySQL
```

---


### Full Code

```csharp
namespace WarrantyTracker.Server.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BrandsController : ControllerBase
    {
        private readonly ILogger<BrandsController> _logger;
        private readonly AppDbContext _appDbContext;
        
        public BrandsController(ILogger<BrandsController> logger, 
                                AppDbContext appDbContext)
        {
            _logger = logger;
            _appDbContext = appDbContext;
        }
        
        // GET: api/<BrandsController>
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            var result = await _appDbContext.Brands.Select(b => new { b.Id, b.Name }).ToListAsync();
        
            return Ok(result);
        }
        
        // GET api/<BrandsController>/5
        [HttpGet("{id}")]
        public async Task<IActionResult> Get(int id)
        {
            var result = await _appDbContext.Brands
                .Where(b => b.Id == id)
                .Select(b => new { b.Id, b.Name })
                .FirstOrDefaultAsync();
        
            if (result == null)
            {
                return NotFound();
            }
        
            return Ok(result);
        }

        // POST api/<BrandsController>
        [HttpPost]
        public void Post([FromBody] string value)
        {
        }

        // PUT api/<BrandsController>/5
        [HttpPut("{id}")]
        public void Put(int id, [FromBody] string value)
        {
        }

        // DELETE api/<BrandsController>/5
        [HttpDelete("{id}")]
        public void Delete(int id)
        {
        }
    }
}
```


---

# Step 6 - API Implementation List

> **Note:** 🟢 indicates endpoints implemented in Version 1 (MVP). Additional endpoints will be added in future versions.

### Brands API

* `GET /api/brands` - Get all brands  🟢
* `GET /api/brands/{id}` - Get brand by ID  🟢
* `POST /api/brands` - Create brand
* `PUT /api/brands/{id}` - Update brand
* `DELETE /api/brands/{id}` - Delete brand

---

### Purchase Sources API

* `GET /api/purchasesources` - Get all purchase sources  🟢
* `GET /api/purchasesources/{id}` - Get purchase source by ID  🟢
* `POST /api/purchasesources` - Create purchase source
* `PUT /api/purchasesources/{id}` - Update purchase source
* `DELETE /api/purchasesources/{id}` - Delete purchase source

---

### Devices API

* `GET /api/devices` - Get all devices  🟢
* `GET /api/devices/{id}` - Get device by ID  🟢
* `GET /api/devices/search?name={name}` - Search devices by name  🟢
* `POST /api/devices` - Create device
* `PUT /api/devices/{id}` - Update device
* `DELETE /api/devices/{id}` - Delete device

---

### Warranty Registrations API

* `GET /api/warrantyregistrations` - Get all warranty registrations  🟢
* `GET /api/warrantyregistrations/{id}` - Get warranty registration by ID  🟢
* `GET /api/warrantyregistrations/mobile/{mobileNumber}` - Get warranty registrations by mobile number  🟢
* `POST /api/warrantyregistrations` - Create warranty registration  🟢
* `PUT /api/warrantyregistrations/{id}` - Update warranty registration
* `DELETE /api/warrantyregistrations/{id}` - Delete warranty registration

---

### API Summary

| Module                 | Endpoints        |
| ---------------------- | ---------------- |
| Brands                 | 5                |
| Purchase Sources       | 5                |
| Devices                | 6                |
| Warranty Registrations | 6                |
| **Total**              | **22 Endpoints** |


---

## Brand

Properties:

```text
Id
Name
CreatedAt
```

---

## PurchaseSource

Properties:

```text
Id
Name
Website
CreatedAt
```

---

## Device

Properties:

```text
Id
Name
BrandId
ModelNumber
SerialNumber
PurchaseDate
PurchaseSourceId
Price
Notes
CreatedAt
```

Navigation Properties:

```text
Brand
PurchaseSource
WarrantyRegistrations
```

---

## WarrantyRegistration

Properties:

```text
Id
DeviceId
WarrantyStart
WarrantyEnd
RegistrationNumber
Remarks
CreatedAt
```

Navigation:

```text
Device
```

---

# Step 7 - Create DTOs

## Brand DTOs

```text
CreateBrandDto
UpdateBrandDto
BrandResponseDto
```

---

## Device DTOs

```text
CreateDeviceDto
UpdateDeviceDto
DeviceResponseDto
```

---

## Purchase Source DTOs

```text
CreatePurchaseSourceDto
UpdatePurchaseSourceDto
PurchaseSourceResponseDto
```

---

## Warranty Registration DTOs

```text
CreateWarrantyRegistrationDto
UpdateWarrantyRegistrationDto
WarrantyRegistrationResponseDto
```

---

# Step 8 - Configure ApplicationDbContext

Responsibilities:

* Inherit DbContext
* Register DbSets
* Configure Relationships
* Configure Foreign Keys

DbSets:

```text
Brands
Devices
PurchaseSources
WarrantyRegistrations
```

---

# Step 9 - Repository Layer

## Brand Repository

Methods:

```text
GetAllAsync()
GetByIdAsync()
CreateAsync()
UpdateAsync()
DeleteAsync()
ExistsAsync()
```

---

## Device Repository

Methods:

```text
GetAllAsync()
GetByIdAsync()
CreateAsync()
UpdateAsync()
DeleteAsync()
ExistsAsync()
```

Include:

```text
Brand
PurchaseSource
WarrantyRegistrations
```

---

## Purchase Source Repository

Methods:

```text
GetAllAsync()
GetByIdAsync()
CreateAsync()
UpdateAsync()
DeleteAsync()
ExistsAsync()
```

---

## Warranty Registration Repository

Methods:

```text
GetAllAsync()
GetByIdAsync()
CreateAsync()
UpdateAsync()
DeleteAsync()
ExistsAsync()
```

---

# Step 10 - Service Layer

## Brand Service

Responsibilities:

```text
Create Brand
Update Brand
Delete Brand
Get Brand
Get All Brands
```

Validation:

```text
Brand name required
Brand name unique
```

---

## Device Service

Responsibilities:

```text
Create Device
Update Device
Delete Device
Get Device
Get All Devices
```

Validation:

```text
Brand must exist
Purchase Source must exist
Price >= 0
```

---

## Purchase Source Service

Responsibilities:

```text
Create Purchase Source
Update Purchase Source
Delete Purchase Source
Get Purchase Source
Get All Purchase Sources
```

---

## Warranty Registration Service

Responsibilities:

```text
Create Warranty
Update Warranty
Delete Warranty
Get Warranty
Get All Warranties
```

Validation:

```text
Device exists

WarrantyStart <= WarrantyEnd
```

---

# Step 11 - Register Dependencies

Register:

```text
ApplicationDbContext

Repositories

Services
```

Dependency Flow:

```text
IBrandRepository
    →
BrandRepository

IBrandService
    →
BrandService
```

Repeat for all entities.

---

# Step 12 - Create Controllers

## Brands Controller

Route:

```http
/api/brands
```

Endpoints:

```http
GET      /api/brands

GET      /api/brands/{id}

POST     /api/brands

PUT      /api/brands/{id}

DELETE   /api/brands/{id}
```

Dependency:

```text
IBrandService
```

---

## Devices Controller

Route:

```http
/api/devices
```

Endpoints:

```http
GET      /api/devices

GET      /api/devices/{id}

POST     /api/devices

PUT      /api/devices/{id}

DELETE   /api/devices/{id}
```

Dependency:

```text
IDeviceService
```

---

## Purchase Sources Controller

Route:

```http
/api/purchasesources
```

Endpoints:

```http
GET      /api/purchasesources

GET      /api/purchasesources/{id}

POST     /api/purchasesources

PUT      /api/purchasesources/{id}

DELETE   /api/purchasesources/{id}
```

Dependency:

```text
IPurchaseSourceService
```

---

## Warranty Registrations Controller

Route:

```http
/api/warrantyregistrations
```

Endpoints:

```http
GET      /api/warrantyregistrations

GET      /api/warrantyregistrations/{id}

POST     /api/warrantyregistrations

PUT      /api/warrantyregistrations/{id}

DELETE   /api/warrantyregistrations/{id}
```

Dependency:

```text
IWarrantyRegistrationService
```

---

# Step 13 - API Response Standard

Success:

```json
{
  "success": true,
  "message": "Operation completed successfully",
  "data": {}
}
```

Error:

```json
{
  "success": false,
  "message": "Resource not found"
}
```

Validation Error:

```json
{
  "success": false,
  "message": "Validation failed",
  "errors": [
    "Brand Name is required"
  ]
}
```

---

# Step 14 - Testing Checklist

## Brands

* Create Brand
* Update Brand
* Delete Brand
* Get Brand
* Get All Brands

---

## Purchase Sources

* Create Purchase Source
* Update Purchase Source
* Delete Purchase Source
* Get Purchase Source
* Get All Purchase Sources

---

## Devices

* Create Device
* Update Device
* Delete Device
* Get Device
* Get All Devices

---

## Warranty Registrations

* Create Warranty
* Update Warranty
* Delete Warranty
* Get Warranty
* Get All Warranties

---

# Final Implementation Flow

<img width="35%" alt="software-architecture-flowchart-with-database" src="https://github.com/user-attachments/assets/0f5f2f3d-869c-49b8-9a1a-ca99ba1c85ba" />

```text
HTTP Request
    ↓
Controller
    ↓
Service
    ↓
Repository
    ↓
ApplicationDbContext
    ↓
MySQL Database
    ↓
Repository
    ↓
Service
    ↓
Controller
    ↓
HTTP Response
```
