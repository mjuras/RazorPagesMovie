This is a tutorial project for experimenting with ASP.NET core 2.2 and Razor using MVC.
====


[The instructions for the tutorial are here.](
https://docs.microsoft.com/en-us/aspnet/core/tutorials/razor-pages/?view=aspnetcore-2.2])

This sample uses entity framework and localDB for persistence.  This bit is pretty mysterious.

Create a Razor Pages web app
---
This was done using dialogs in VS2017.  New project; Web App [ASp.NET Core 2.2]
The resulting folder containd quite a bit of content and is in this repo as the initial commit.

Note: it includes support for  European Union's General Data Protection Regulation (GDPR). which is something I need to add to my site.   This can be turned on and off in the code:

	RazorPagesMovie\Startup.cs(31):
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;

RazorPagesMovie.Program.Main is the program launch point.

The menu layout is implemented in the Pages/Shared/_Layout.cshtml file. Open the Pages/Shared/_Layout.cshtml file.

Add a model to a Razor Pages app 
---

> **Model is short for data Model** it is the classes that define the information managed by the application.

> public int ID { get; set; } is required by the application as it is the Primary Key for the entity instances.

> System.ComponentModel.DataAnnotations is a namespace that facilitates declarative formatting for implicit conversions to string/display and persistence.  For example DataType(DataType.Date)]: The DataType attribute specifies the type of the data (Date). With this attribute: 
> - The user is not required to enter time information in the date field. 
> - Only the date i displayed, not time information.

In this step, I also generate a CRUD scaffolding for the Movie Entity.  This cranks out 10 files 2 for each CRUD and 2 for List (Index):

	RazorPagesMovie\Pages\Movies\Create.cshtml Create.cshtml.cs
	RazorPagesMovie\Pages\Movies\Delete.cshtml Delete.cshtml.cs
	RazorPagesMovie\Pages\Movies\Details.cshtml Details.cshtml.cs
	RazorPagesMovie\Pages\Movies\Edit.cshtml Edit.cshtml.cs
	RazorPagesMovie\Pages\Movies\Index.cshtml Index.cshtml.cs    

The Scaffolding request also generated a Data/RazorPagesMovieContext.cs that has a 
Microsoft.EntityFrameworkCore.DbSet for Movie:

	public DbSet<RazorPagesMovie.Models.Movie> Movie { get; set; }

The scaffolding also changed Startup.cs adding his data context:

    services.AddDbContext<RazorPagesMovieContext>(options =>
                    options.UseSqlServer(Configuration.GetConnectionString("RazorPagesMovieContext")));

### Database Setup
Now: this is code first development, so we still need to create corresponding structures in the server.  Entity Framework can do this based on the project code.  This is referred to as a Migration of code to the database.

First I need to create an initial database:

The `ef migrations add InitialCreate` command generates code to create the initial database schema. The schema is based on the model specified in the DbContext (In the RazorPagesMovieContext.cs file). The InitialCreate argument is used to name the migrations. Any name can be used, but by convention a name is selected that describes the migration.

The `ef database update` command runs the Up method in the Migrations/<time-stamp>_InitialCreate.cs file. The Up method creates the database.

In Package Manager Console

      PM> cd .\RazorPagesMovie
      PM> dotnet ef migrations add InitialCreate
      warn: Microsoft.EntityFrameworkCore.Model.Validation[30000]
            No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.
      info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
            Entity Framework Core 2.2.1-servicing-10028 initialized 'RazorPagesMovieContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
      Done. To undo this action, use 'ef migrations remove'
----
      PM> dotnet ef database update
      warn: Microsoft.EntityFrameworkCore.Model.Validation[30000]
            No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.
      info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
            Entity Framework Core 2.2.1-servicing-10028 initialized 'RazorPagesMovieContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
      info: Microsoft.EntityFrameworkCore.Database.Command[20101]
            Executed DbCommand (226ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
            CREATE DATABASE [RazorPagesMovieContext-d2893e8a-0415-46c5-97c0-34c33fe1b403];
      info: Microsoft.EntityFrameworkCore.Database.Command[20101]
            Executed DbCommand (44ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
            IF SERVERPROPERTY('EngineEdition') <> 5
            BEGIN
                  ALTER DATABASE [RazorPagesMovieContext-d2893e8a-0415-46c5-97c0-34c33fe1b403] SET READ_COMMITTED_SNAPSHOT ON;
            END;
      info: Microsoft.EntityFrameworkCore.Database.Command[20101]
            Executed DbCommand (10ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
            CREATE TABLE [__EFMigrationsHistory] (
                  [MigrationId] nvarchar(150) NOT NULL,
                  [ProductVersion] nvarchar(32) NOT NULL,
                  CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
            );
      info: Microsoft.EntityFrameworkCore.Database.Command[20101]
            Executed DbCommand (6ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
            SELECT OBJECT_ID(N'[__EFMigrationsHistory]');
      info: Microsoft.EntityFrameworkCore.Database.Command[20101]
            Executed DbCommand (2ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
            SELECT [MigrationId], [ProductVersion]
            FROM [__EFMigrationsHistory]
            ORDER BY [MigrationId];
      info: Microsoft.EntityFrameworkCore.Migrations[20402]
            Applying migration '20190202211058_InitialCreate'.
      Applying migration '20190202211058_InitialCreate'.
      info: Microsoft.EntityFrameworkCore.Database.Command[20101]
            Executed DbCommand (2ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
            CREATE TABLE [Movie] (
                  [ID] int NOT NULL IDENTITY,
                  [Title] nvarchar(max) NULL,
                  [ReleaseDate] datetime2 NOT NULL,
                  [Genre] nvarchar(max) NULL,
                  [Price] decimal(18,2) NOT NULL,
                  CONSTRAINT [PK_Movie] PRIMARY KEY ([ID])
            );
      info: Microsoft.EntityFrameworkCore.Database.Command[20101]
            Executed DbCommand (2ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
            INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
            VALUES (N'20190202211058_InitialCreate', N'2.2.1-servicing-10028');
      Done.

This created the following commands.

There is also a database found in my localDB server:

	RazorPagesMovieContext-d2893e8a-0415-46c5-97c0-34c33fe1b403
		tables: Movies and __EFMigrationsHistory

There is one record in the __EFMigrationsHistory.  This table is used to check if the DB is in sync with the code.  When using EF Code First to automatically create a database, Code First:

* Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.  *
* If the model classes aren't in sync with the DB, EF throws an exception.

Scaffold (generate) Razor pages
---
### The CRUDL Page classes
Razor Pages are derived from PageModel. By convention, the PageModel-derived class is called <PageName>Model. The constructor uses dependency injection to add the RazorPagesMovieContext to the page. All the scaffolded pages follow this pattern.

When a request is made for the page, the OnGetAsync method returns a list of movies to the Razor Page. OnGetAsync or OnGet is called on a Razor Page to initialize the state for the page. In this case, OnGetAsync gets a list of movies and displays them.

      Pages\Movies\Create.cshtml.cs(21):        public IActionResult OnGet()
      Pages\Movies\Create.cshtml.cs(29):        public async Task<IActionResult> OnPostAsync()
      Pages\Movies\Delete.cshtml.cs(24):        public async Task<IActionResult> OnGetAsync(int? id)
      Pages\Movies\Delete.cshtml.cs(40):        public async Task<IActionResult> OnPostAsync(int? id)
      Pages\Movies\Details.cshtml.cs(23):       public async Task<IActionResult> OnGetAsync(int? id)
      Pages\Movies\Edit.cshtml.cs(25):          public async Task<IActionResult> OnGetAsync(int? id)
      Pages\Movies\Edit.cshtml.cs(41):          public async Task<IActionResult> OnPostAsync()
      Pages\Movies\Index.cshtml.cs(23):         public async Task OnGetAsync()
      Pages\Error.cshtml.cs(18):                public void OnGet()
      Pages\Index.cshtml.cs(12):                public void OnGet()
      Pages\Privacy.cshtml.cs(12):              public void OnGet()


When OnGet returns void or OnGetAsync returnsTask, no return method is used. When the return type is IActionResult or Task<IActionResult>, a return statement must be provided. For example, the Pages/Movies/Create.cshtml.cs OnPostAsync method:

      return RedirectToPage("./Index");

### The CRUDL Markup (cshtml)

Examine the `Pages/Movies/Index.cshtml` Razor Page:

      @page
      @model RazorPagesMovie.Pages.Movies.IndexModel

The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests. @page must be the first Razor directive on a page. 
The `@model` directive specifies the type of the model passed to the Razor Page. 

The Movie property uses the [BindProperty] attribute to opt-in to model binding. When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the Movie model.

      [BindProperty]
        public Movie Movie { get; set; }


Razor can transition from HTML into C# or into Razor-specific markup. When an @ symbol is followed by a Razor reserved keyword, it transitions into Razor-specific markup, otherwise it transitions into C#.

Work with a database
---
The database context is registered with the Dependency Injection container in the ConfigureServices method in Startup.cs:

      public void ConfigureServices(IServiceCollection services)
      {
      services.Configure<CookiePolicyOptions>...
      services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
      services.AddDbContext<RazorPagesMovieContext>(options =>
            options.UseSqlServer(
            Configuration.GetConnectionString("RazorPagesMovieContext")));
      }

in appsettings.json we hacve

    "ConnectionStrings": {
    "RazorPagesMovieContext": "Server=(localdb)\\mssqllocaldb;Database=RazorPagesMovieContext-d2893e8a-0415-46c5-97c0-34c33fe1b403;Trusted_Connection=True;    MultipleActiveResultSets=true"


LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development. LocalDB starts on demand and runs in user mode, so there's no complex configuration. By default, LocalDB database creates *.mdf files in the C:/Users/<user/> directory.
> C:\Users\mark\AppData\Local\Microsoft\Microsoft SQL Server Local DB\Instances\MSSQLLocalDB

The rest of this section is about adding a helper class that uses EF conventions to load the Movie table.  


Update Razor pages
---
Add search
---
Add a new field
---
Add validation
---


[More on markdown](https://www.markdownguide.org/basic-syntax/)