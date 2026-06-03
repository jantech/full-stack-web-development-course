# Warranty Tracker API - .NET 8 Web API Implementation Guide

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

```bash
dotnet new webapi -n WarrantyTracker.Api
cd WarrantyTracker.Api
```

Remove sample WeatherForecast files if not required.

---

# Step 2 - Install Required Packages

```bash
dotnet add package Microsoft.EntityFrameworkCore

dotnet add package Microsoft.EntityFrameworkCore.Design

dotnet add package Pomelo.EntityFrameworkCore.MySql
```

Optional:

```bash
dotnet add package Mapster

dotnet add package FluentValidation.AspNetCore

dotnet add package Swashbuckle.AspNetCore
```

---

# Step 3 - Database Design

## Brands

```sql
CREATE TABLE brands (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(128) NOT NULL UNIQUE,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

## Purchase Sources

```sql
CREATE TABLE purchase_sources (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(128) NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

## Devices

```sql
CREATE TABLE devices (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(128) NOT NULL,
    brand_id INT NOT NULL,
    model_number VARCHAR(50),
    warranty_months INT NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_device_brand
        FOREIGN KEY (brand_id)
        REFERENCES brands(id)
        ON DELETE CASCADE
);
```

## Warranty Registrations

```sql
CREATE TABLE user_warranty_register (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,

    owner_name VARCHAR(128) NOT NULL,
    email_address VARCHAR(100),
    mobile_number VARCHAR(15) NOT NULL,

    device_id INT NOT NULL,
    purchase_source_id INT,

    purchase_date DATE NOT NULL,
    warranty_start DATE NOT NULL,

    invoice_file VARCHAR(255),
    notes TEXT,

    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_register_device
        FOREIGN KEY (device_id)
        REFERENCES devices(id),

    CONSTRAINT fk_register_source
        FOREIGN KEY (purchase_source_id)
        REFERENCES purchase_sources(id)
);
```

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

# Step 5 - Configure Database Connection

## appsettings.json

Example:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "server=localhost;database=warranty_tracker;user=root;password=yourpassword"
  }
}
```

---

# Step 6 - Create Entity Models

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
