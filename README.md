# MiniStore

MiniStore is an ASP.NET Core MVC inventory management application built with C#, Entity Framework Core, SQL Server, and ASP.NET Core Identity.

## Features

- Product CRUD operations
- SQL Server database integration
- Entity Framework Core
- Code First migrations
- Bootstrap responsive UI
- Server-side validation
- Client-side validation
- Bootstrap delete confirmation modal
- ASP.NET Core Identity
- User registration and login
- Secure password hashing
- Authentication and authorization
- Admin and User roles
- Seeded Admin account
- Role-based access control

## Technologies Used

- C#
- ASP.NET Core MVC
- ASP.NET Core Identity
- Entity Framework Core
- SQL Server
- Razor Pages
- Bootstrap
- jQuery
- HTML
- CSS

## Project Structure

    MiniStore/
    │
    ├── Controllers/
    │   ├── HomeController.cs
    │   └── ProductsController.cs
    │
    ├── Data/
    │   ├── ApplicationDbContext.cs
    │   └── Migrations/
    │
    ├── Models/
    │   └── Product.cs
    │
    ├── Views/
    │   ├── Home/
    │   ├── Products/
    │   └── Shared/
    │
    ├── Areas/
    │   └── Identity/
    │
    ├── wwwroot/
    │
    ├── appsettings.json
    └── Program.cs

## Database

MiniStore uses SQL Server with Entity Framework Core.

The `ApplicationDbContext` inherits from `IdentityDbContext`, allowing both application data and ASP.NET Core Identity data to be stored in the same database.

    public class ApplicationDbContext : IdentityDbContext
    {
        public ApplicationDbContext(
            DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        public DbSet<Product> Products { get; set; }
    }

### Main Tables

Product information is stored in the `Products` table.

ASP.NET Core Identity creates and manages tables such as:

- `AspNetUsers`
- `AspNetRoles`
- `AspNetUserRoles`
- `AspNetUserClaims`
- `AspNetUserLogins`
- `AspNetUserTokens`
- `AspNetRoleClaims`

## Product Model

The `Product` model contains:

- `Id`
- `Name`
- `Price`
- `Quantity`

Validation is implemented using Data Annotations.

    [Required]
    [StringLength(100)]
    public string Name { get; set; }

    [Range(0.01, 10000000)]
    public decimal Price { get; set; }

    [Range(0, 100000)]
    public int Quantity { get; set; }

## CRUD Operations

MiniStore implements the complete CRUD workflow:

    Create
       ↓
     Read
       ↓
     Update
       ↓
     Delete

Products are retrieved from the database using Entity Framework Core:

    var products = await _context.Products.ToListAsync();

Changes are persisted using:

    await _context.SaveChangesAsync();

## Authentication

ASP.NET Core Identity handles user registration, login, logout, and user management.

Passwords are not stored as plain text. ASP.NET Core Identity hashes passwords before storing them in the `AspNetUsers` table.

Identity is configured using:

    builder.Services.AddDefaultIdentity<IdentityUser>(
        options => options.SignIn.RequireConfirmedAccount = false
    )
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();

Authentication is handled using Identity's authentication cookies.

## Authorization

Authenticated users can be restricted using the `[Authorize]` attribute.

    [Authorize]
    public class ProductsController : Controller
    {
    }

This means that only authenticated users can access the controller.

Role-based authorization can restrict specific actions to administrators:

    [Authorize(Roles = "Admin")]

This requires the user to be both authenticated and assigned to the `Admin` role.

## Roles

MiniStore uses two roles:

- `Admin`
- `User`

The roles are created automatically when the application starts if they do not already exist.

An initial Admin account is also seeded.

### Access Control

| Feature | User | Admin |
|---|---|---|
| View Products | Yes | Yes |
| Create Product | No | Yes |
| Edit Product | No | Yes |
| Delete Product | No | Yes |

## Role Relationships

ASP.NET Core Identity uses a many-to-many relationship between users and roles.

The relationship is managed through the `AspNetUserRoles` table.

    AspNetUsers
         │
         │
         ▼
    AspNetUserRoles
         │
         │
         ▼
    AspNetRoles

This allows one user to have multiple roles and one role to be assigned to multiple users.

## Validation

MiniStore implements both server-side and client-side validation.

### Server-side Validation

The controller checks whether the submitted model is valid before saving it:

    if (ModelState.IsValid)
    {
        _context.Products.Add(product);
        await _context.SaveChangesAsync();
    }

### Client-side Validation

Razor validation helpers are used to display validation errors directly beside form fields:

    <span asp-validation-for="Quantity"
          class="text-danger">
    </span>

For example, a quantity below `0` will trigger the validation rule defined in the Product model.

## Delete Confirmation

Product deletion uses a Bootstrap modal to ask the user for confirmation before submitting the delete request.

The modal displays:

- The product name
- A confirmation message
- A Cancel button
- A Delete Product button

## Dependency Injection

MiniStore uses ASP.NET Core's built-in Dependency Injection system.

For example, `ApplicationDbContext` is injected into `ProductsController`:

    private readonly ApplicationDbContext _context;

    public ProductsController(ApplicationDbContext context)
    {
        _context = context;
    }

Identity services such as `UserManager` and `RoleManager` are also provided through Dependency Injection.

## Getting Started

### Prerequisites

Make sure you have the following installed:

- .NET SDK
- Visual Studio
- SQL Server
- SQL Server Management Studio

### Clone the Repository

    git clone https://github.com/your-username/MiniStore.git

Navigate to the project directory:

    cd MiniStore

### Configure the Database

Update the connection string in `appsettings.json`:

    "ConnectionStrings": {
        "DefaultConnection": "Server=YOUR_SERVER;Database=MiniStore;Trusted_Connection=True;TrustServerCertificate=True;"
    }

Replace `YOUR_SERVER` with your SQL Server instance.

### Apply Migrations

Using Visual Studio Package Manager Console:

    Update-Database

Or using the .NET CLI:

    dotnet ef database update

### Run the Application

    dotnet run

The application can also be run directly through Visual Studio.

## Seeded Admin Account

For development purposes, MiniStore creates an initial Admin account:

    Email: admin@ministore.com
    Password: Admin@12345

The Admin account is automatically assigned to the `Admin` role.

Do not use these credentials in a production environment. Production credentials should be stored securely using secrets, environment variables, or another secure configuration system.

## Authentication and Authorization Flow

The authentication flow works as follows:

    User
      ↓
    Login
      ↓
    ASP.NET Core Identity
      ↓
    Password Verification
      ↓
    Authentication Cookie
      ↓
    HttpContext.User
      ↓
    Authorization
      ↓
    Protected Resource

Authentication determines who the user is, while authorization determines what the authenticated user is allowed to access.

## Future Improvements

- User management dashboard
- Admin-controlled role management
- Product search
- Product filtering
- Pagination
- jQuery DataTables
- ViewModels
- Improved error handling
- Production-ready secret management
- Cloud deployment

## Learning Goals

This project is being developed to gain practical experience with:

- ASP.NET Core MVC
- Dependency Injection
- Entity Framework Core
- SQL Server
- Code First migrations
- CRUD operations
- Razor Views
- Bootstrap
- jQuery
- Client-side validation
- Server-side validation
- Authentication
- Authorization
- Role-based authorization
- ASP.NET Core Identity
- Password hashing
- Authentication cookies
- Middleware
- Many-to-many relationships
