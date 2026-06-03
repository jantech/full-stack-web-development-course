# Warranty Tracker Database

A simple MySQL database schema for tracking product warranties, purchase information, and warranty status.

## Features

* Brand management
* Device catalog
* Purchase source tracking
* Warranty registration
* Warranty expiry calculation
* Active / Expired / Expiring Soon status reporting

---


## Database Structure

### Brands

Stores manufacturer information.

| Column     | Type         | Description          |
| ---------- | ------------ | -------------------- |
| id         | INT          | Primary Key          |
| name       | VARCHAR(128) | Brand Name           |
| created_at | DATETIME     | Record Creation Date |


```sql
CREATE TABLE brands (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(128) NOT NULL UNIQUE,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### Sample Data

```sql
INSERT INTO brands (name) VALUES
('Samsung'),
('LG'),
('Sony'),
('Apple'),
('Dell'),
('HP'),
('Lenovo'),
('OnePlus'),
('Xiaomi'),
('Whirlpool');
```

---

### Devices

Master catalog of devices and warranty periods.

| Column          | Type         | Description          |
| --------------- | ------------ | -------------------- |
| id              | INT          | Primary Key          |
| name            | VARCHAR(128) | Device Name          |
| brand_id        | INT          | Brand Reference      |
| model_number    | VARCHAR(50)  | Model Number         |
| warranty_months | INT          | Warranty Duration    |
| created_at      | DATETIME     | Record Creation Date |

**Relationship**

```text
Brands (1) ----> (Many) Devices
```


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

### Sample Data

```sql
INSERT INTO devices
(name, brand_id, model_number, warranty_months)
VALUES

('Galaxy S24', 1, 'SM-S921B', 24),
('Galaxy A55', 1, 'SM-A556E', 12),

('Front Load Washing Machine', 2, 'FHM1207ZDL', 24),
('Smart Refrigerator', 2, 'GL-T292RPZY', 36),

('Bravia Smart TV 55"', 3, 'KD-55X74K', 24),
('PlayStation 5', 3, 'CFI-1208A', 12),

('iPhone 15', 4, 'A3090', 12),
('MacBook Air M3', 4, 'MXCU3HN/A', 12),

('Inspiron 15', 5, 'INS15-3530', 12),

('Pavilion 14', 6, '14-EC1003AU', 12),

('ThinkPad E14', 7, 'E14 Gen 5', 36),

('OnePlus 12', 8, 'CPH2573', 12),

('Redmi Note 13 Pro', 9, '2312DRAABG', 12),

('Double Door Refrigerator', 10, 'IF305 ELT', 36);
```

---

### Purchase Sources

Stores where the device was purchased.

Examples:

* Amazon
* Flipkart
* Croma
* Reliance Digital
* Local Retail Shop

| Column     | Type         |
| ---------- | ------------ |
| id         | INT          |
| name       | VARCHAR(128) |
| created_at | DATETIME     |


```sql
CREATE TABLE purchase_sources (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(128) NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### Sample Data

```sql
INSERT INTO purchase_sources (name) VALUES
('Amazon'),
('Flipkart'),
('Reliance Digital'),
('Croma'),
('Poorvika'),
('Vijay Sales'),
('Samsung Store'),
('Apple Store'),
('Local Retail Shop');
```


---

### Warranty Registrations

Represents an actual purchased product owned by a user.

| Column             | Type         |
| ------------------ | ------------ |
| id                 | BIGINT       |
| owner_name         | VARCHAR(128) |
| email_address      | VARCHAR(100) |
| mobile_number      | VARCHAR(15)  |
| device_id          | INT          |
| purchase_source_id | INT          |
| purchase_date      | DATE         |
| warranty_start     | DATE         |
| invoice_file       | VARCHAR(255) |
| notes              | TEXT         |
| created_at         | DATETIME     |

**Relationship**

```text
Devices (1) ------------> (Many) Warranty Registrations
Purchase Sources (1) ---> (Many) Warranty Registrations
```


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

### Sample Data

```sql
INSERT INTO user_warranty_register
(
    owner_name, email_address, mobile_number, device_id, purchase_source_id, purchase_date, warranty_start 
)
VALUES
('Jaganathan','jaganathan@example.com','9876543210',1,1,'2025-09-15','2025-09-15'),
('Jaganathan','jaganathan@example.com','9876543210',3,2,'2025-07-01','2025-07-05'),
('Jaganathan','jaganathan@example.com','9876543210',7,8,'2025-06-20','2025-06-20'),
('Priya','priya@example.com','9123456780',11,3,'2024-08-10','2024-08-15'),
('Arun','arun@example.com','9345678901',5,4,'2024-06-01','2024-06-01'),
('Karthik','karthik@example.com','9567890123',8,1,'2025-12-01','2025-12-01'),
('Meena','meena@example.com','9789012345',13,2,'2025-10-15','2025-10-15'),
('Suresh','suresh@example.com','9890123456',14,6,'2024-01-20','2024-01-25');
```


---

## Warranty Calculation

Warranty end date is calculated dynamically.

```sql
DATE_ADD(
    warranty_start,
    INTERVAL warranty_months MONTH
)
```

### Warranty Status Rules

| Condition                   | Status        |
| --------------------------- | ------------- |
| End Date < Today            | Expired       |
| End Date <= Today + 30 Days | Expiring Soon |
| Otherwise                   | Active        |

---

## Sample Query: Warranty Status

```sql
SELECT
    r.id,
    r.owner_name,
    b.name AS brand_name,
    d.name AS device_name,
    d.model_number,
    r.purchase_date,
    r.warranty_start,
    d.warranty_months,

    DATE_ADD(
        r.warranty_start,
        INTERVAL d.warranty_months MONTH
    ) AS warranty_end,

    CASE
        WHEN DATE_ADD(
            r.warranty_start,
            INTERVAL d.warranty_months MONTH
        ) < CURDATE()
        THEN 'Expired'

        WHEN DATE_ADD(
            r.warranty_start,
            INTERVAL d.warranty_months MONTH
        ) <= DATE_ADD(CURDATE(), INTERVAL 30 DAY)
        THEN 'Expiring Soon'

        ELSE 'Active'
    END AS warranty_status

FROM user_warranty_register r
INNER JOIN devices d
    ON r.device_id = d.id
INNER JOIN brands b
    ON d.brand_id = b.id
ORDER BY warranty_end;
```


## Explanation

| Column          | Description                          |
| --------------- | ------------------------------------ |
| owner_name      | Device owner                         |
| brand_name      | Device brand                         |
| device_name     | Product name                         |
| model_number    | Model number                         |
| purchase_date   | Date purchased                       |
| warranty_start  | Warranty start date                  |
| warranty_months | Warranty duration from device master |
| warranty_end    | Calculated expiry date               |
| warranty_status | Active / Expiring Soon / Expired     |






