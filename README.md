# CMCS-API
CMCS-API

A .NET 8 Web API for a Contract Monthly Claim System (CMCS). Lecturers submit claims for hours worked, coordinators verify them, and managers approve them. Data is stored in SQL Server LocalDB using Entity Framework Core.
Technologies
C# / ASP.NET Core Web API (.NET 8)
Entity Framework Core 8.0.6 (SQL Server, code-first migrations)
SQL Server LocalDB ((localdb)\MSSQLLocalDB)
Swagger (Swashbuckle) for testing endpoints
xUnit + EF Core InMemory provider for unit tests
Project structure
CMCS-API/
├── Controllers/
│   ├── AuthController.cs        (login)
│   ├── ClaimController.cs       (submit, update, verify, reject, approve, delete)
│   ├── DocumentsController.cs   (upload, view, delete supporting documents)
│   └── LecturerController.cs    (register, view and update profile)
├── Data/
│   └── CMCS_APIContext.cs       (DbContext + seed data)
├── Migrations/                  (InitialCreate, SeedData)
├── Models/
│   ├── Claim.cs
│   ├── Coordinator.cs
│   ├── Lecturer.cs
│   ├── Manager.cs
│   └── Supporting_Document.cs
├── appsettings.json
├── CMCS-API.http                (request file for testing)
└── Program.cs

CMCS-API.Tests/
└── ControllerTests.cs           (success and failure unit tests)
Database models
Model	Fields
Lecturer	LecturerID, FullName, Email, Password
Claim	ClaimID, LecturerID, HoursWorked, HourlyRate, TotalAmount, ClaimStatus
Supporting_Document	DocumentID, ClaimID, FileName
Coordinator	CoordinatorID, FullName, Email
Manager	ManagerID, FullName, Email

Claim status flow: Pending → Verified → Approved (or Rejected).
