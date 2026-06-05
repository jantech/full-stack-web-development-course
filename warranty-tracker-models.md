
# Warranty Tracker Models & DTOs

## Brands

```csharp

[Table("brands")]
public class Brand
{
    [Key]
    [Column("id")]
    public int Id { get; set; }

    [Required]
    [StringLength(128)]
    [Column("name")]
    public string Name { get; set; } = string.Empty;

    [Column("created_at")]
    public DateTime CreatedAt { get; set; }

    // Navigation Property
    public ICollection<Device> Devices { get; set; } = new List<Device>();
}

```

## Devices

```csharp

[Table("devices")]
public class Device
{
    [Key]
    [Column("id")]
    public int Id { get; set; }

    [Required]
    [StringLength(128)]
    [Column("name")]
    public string Name { get; set; } = string.Empty;

    [Required]
    [Column("brand_id")]
    public int BrandId { get; set; }

    [StringLength(50)]
    [Column("model_number")]
    public string? ModelNumber { get; set; }

    [Required]
    [Column("warranty_months")]
    public int WarrantyMonths { get; set; }

    [Column("created_at")]
    public DateTime CreatedAt { get; set; }

    // Navigation Property
    [ForeignKey(nameof(BrandId))]
    public Brand Brand { get; set; } = null!;

    public ICollection<UserWarrantyRegister> WarrantyRegistrations { get; set; } = new List<UserWarrantyRegister>();

}

```


## PurchaseSources

```csharp

[Table("purchase_sources")]
public class PurchaseSource
{
    [Key]
    [Column("id")]
    public int Id { get; set; }

    [Required]
    [MaxLength(128)]
    [Column("name")]
    public string Name { get; set; } = string.Empty;

    [Column("created_at")]
    public DateTime CreatedAt { get; set; }

    public ICollection<UserWarrantyRegister> WarrantyRegistrations { get; set; } = new List<UserWarrantyRegister>();

}

```


## UserWarrantyRegisters

```csharp

[Table("user_warranty_register")]
public class UserWarrantyRegister
{
    [Key]
    [Column("id")]
    public long Id { get; set; }

    [Required]
    [StringLength(128)]
    [Column("owner_name")]
    public string OwnerName { get; set; } = string.Empty;

    [StringLength(100)]
    [Column("email_address")]
    public string? EmailAddress { get; set; }

    [Required]
    [StringLength(15)]
    [Column("mobile_number")]
    public string MobileNumber { get; set; } = string.Empty;

    [Required]
    [Column("device_id")]
    public int DeviceId { get; set; }

    [Column("purchase_source_id")]
    public int? PurchaseSourceId { get; set; }

    [Required]
    [Column("purchase_date")]
    public DateTime PurchaseDate { get; set; }

    [Required]
    [Column("warranty_start")]
    public DateTime WarrantyStart { get; set; }

    [StringLength(255)]
    [Column("invoice_file")]
    public string? InvoiceFile { get; set; }

    [Column("notes")]
    public string? Notes { get; set; }

    [Column("created_at")]
    public DateTime CreatedAt { get; set; }

    // Navigation Properties

    [ForeignKey(nameof(DeviceId))]
    public Device Device { get; set; } = null!;

    [ForeignKey(nameof(PurchaseSourceId))]
    public PurchaseSource? PurchaseSource { get; set; }
}

```

---

## UserWarrantyRegisters DTO

```csharp

public class CreateWarrantyRegistrationRequest
{
    [Required]
    [StringLength(128)]
    public string OwnerName { get; set; } = string.Empty;

    [EmailAddress]
    [StringLength(100)]
    public string? EmailAddress { get; set; }

    [Required]
    [StringLength(15)]
    public string MobileNumber { get; set; } = string.Empty;

    [Required]
    public int DeviceId { get; set; }

    public int? PurchaseSourceId { get; set; }

    [Required]
    public DateTime PurchaseDate { get; set; }

    public string? Notes { get; set; }

    public IFormFile? InvoiceFile { get; set; }
}

```

---

## AppDbContext 

```csharp

    public class AppDbContext : DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
        {
        }
        public DbSet<Brand> Brands { get; set; }
        public DbSet<Device> Devices { get; set; }
        public DbSet<PurchaseSource> PurchaseSources { get; set; }
        public DbSet<UserWarrantyRegister> UserWarrantyRegisters { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);
        }
    }


```
