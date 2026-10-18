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
API endpoints
Auth
Method	Route	Description
POST	/api/auth/login	Log in with email and password (200 or 401)
Lecturers
Method	Route	Description
GET	/api/lecturers	List all lecturers
POST	/api/lecturers/register	Register a lecturer (201)
GET	/api/lecturers/profile/{id}	View a profile (200 or 404)
PUT	/api/lecturers/profile/{id}	Update name and email (200 or 404)
Claims
Method	Route	Description
GET	/api/claims	List all claims
GET	/api/claims/{id}	Get one claim (200 or 404)
POST	/api/claims	Submit a claim. Total is calculated, status set to Pending (201)
PUT	/api/claims/{id}	Update hours and rate, total recalculated
DELETE	/api/claims/{id}	Delete a claim (204 or 404)
GET	/api/claims/pending	List pending claims
GET	/api/claims/verified	List verified claims
PUT	/api/claims/{id}/verify	Mark as Verified
PUT	/api/claims/{id}/reject	Mark as Rejected
PUT	/api/claims/{id}/approve	Mark as Approved
Supporting documents
Method	Route	Description
POST	/api/claims/{id}/documents	Upload a file (saves the file name)
GET	/api/claims/{id}/documents	List documents for a claim
DELETE	/api/documents/{id}	Delete a document
How to run it
Open the solution in Visual Studio 2022 or later.
Check appsettings.json has the correct connection string name:
json
   "ConnectionStrings": {
     "DefaultConnection": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=CMCS_API;Integrated Security=True;Pooling=False;Encrypt=False;Trust Server Certificate=True"
   }
Open Tools > NuGet Package Manager > Package Manager Console, set the Default project to CMCS-API, and run:
   Update-Database
Press F5 and open https://localhost:7269/swagger to test the endpoints.

If you get duplicate key errors, run Drop-Database and then Update-Database to rebuild with the seed data.

Seed data

The database is created with sample data from OnModelCreating in CMCS_APIContext.cs.

Lecturer	Email	Password
John Smith	john.smith@college.com	132435
Sarah Johnson	sarah.j@college.com	67890
Peter Molefe	p.molefe@college.com	12345

It also seeds two claims, two supporting documents, one coordinator and one manager.

Unit tests

The CMCS-API.Tests project uses xUnit and an in-memory database, so the tests never touch the real SQL Server data. Each test creates its own fresh database.

Success tests check valid actions return 200, 201 or 204 and that data really changes (totals calculated, statuses updated).
Failure tests check bad input returns the correct error: 404 for missing records, 401 for a wrong password, 400 for a missing file.

To run them: build the solution (Ctrl+Shift+B), open Test > Test Explorer and click Run All Tests.

Setup used: project reference to CMCS-API and the NuGet package Microsoft.EntityFrameworkCore.InMemory 8.0.6.

What was done, step by step
Created the Web API project with models for Lecturer, Claim, Supporting_Document, Coordinator and Manager.
Set up CMCS_APIContext with a DbSet for each model and registered it in Program.cs.
Created the InitialCreate migration.
Fixed the connection string typo (DeaultConnection to DefaultConnection) so Update-Database could reach SQL Server.
Rewrote the controllers to use the database instead of static in-memory lists, and fixed the routes (api/claims, api/lecturers, api/auth).
Added 404 NotFound handling for records that do not exist and removed unused request bodies from verify, reject and approve.
Added seed data with HasData and ran the SeedData migration.
Removed the default WeatherForecast files.
Fixed the CMCS-API.http file (correct routes, JSON syntax, blank line before bodies, multipart upload).
Wrote unit tests for success and failure cases and ran them with Test Explorer.
Known limitations and future improvements
Passwords are stored in plain text. They should be hashed.
There is no JWT authentication or role-based access yet, so any user can call any endpoint.
No foreign key relationships are defined between Claim, Lecturer and Supporting_Document.
No input validation attributes yet (for example [Range] on HoursWorked).
Uploaded files are not stored on disk, only the file name is saved.
Login returns the full lecturer record, including the password.
AI assistance
NBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
I used Claude (an AI assistant made by Anthropic) to help me check errors while building this project, such as the connection string problem and migration errors. Claude also gave suggestions and example code during troubleshooting, which I reviewed, tested and adapted for this project.
NBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
