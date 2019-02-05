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

> Model binding in ASP.NET Core MVC maps data from HTTP requests to action method parameters. The parameters may be simple types such as strings, integers, or floats, or they may be complex types. This is a great feature of MVC because mapping incoming data to a counterpart is an often repeated scenario, regardless of size or complexity of the data. MVC solves this problem by abstracting binding away so developers don't have to keep rewriting a slightly different version of that same code in every app. Writing your own text to type converter code is tedious, and error prone.

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

in appsettings.json we have

    "ConnectionStrings": {
    "RazorPagesMovieContext": "Server=(localdb)\\mssqllocaldb;Database=RazorPagesMovieContext-d2893e8a-0415-46c5-97c0-34c33fe1b403;Trusted_Connection=True;    MultipleActiveResultSets=true"

LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development. LocalDB starts on demand and runs in user mode, so there's no complex configuration. By default, LocalDB database creates *.mdf files in the C:/Users/<user/> directory.
> C:\Users\mark\AppData\Local\Microsoft\Microsoft SQL Server Local DB\Instances\MSSQLLocalDB

The database can be viewed by selecting **From the View menu, open SQL Server Object Explorer (SSOX).**

The rest of this section is about adding a helper class that uses EF conventions to load the Movie table.  


Update Razor pages
---

### @page directive modification for cleaner url 

Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template. Change the page directive for each of these pages from 

      @page
      to
      @page "{id:int?}"

This allows cleaner URL for these pages for example

http://localhost:5000/Movies/Details/[i] where i is an id Movie.ID value

### concurrency exception handling

There is a special exception type to detect concurrency exceptions:

      try
        {
            await _context.SaveChangesAsync();
        }
        catch (DbUpdateConcurrencyException)


Add search
---
There are several special attributes to allow binding form elemetns to the model:

      [BindProperty(SupportsGet = true)]  public string SearchString { get; set; }
      [BindProperty(SupportsGet = true)]  public string MovieGenre { get; set; }

In the following sections, searching movies by genre or title is added.

      @page "{searchString?}"
      ...
      
      public async Task OnGetAsync()
      {
      // Use LINQ to get list of genres.
      IQueryable<string> genreQuery = from m in _context.Movie
                                          orderby m.Genre
                                          select m.Genre;

      var movies = from m in _context.Movie
                  select m;

      if (!string.IsNullOrEmpty(SearchString))
      {
            movies = movies.Where(s => s.Title.Contains(SearchString));
      }

      if (!string.IsNullOrEmpty(MovieGenre))
      {
            movies = movies.Where(x => x.Genre == MovieGenre);
      }
      Genres = new SelectList(await genreQuery.Distinct().ToListAsync());
      Movie = await movies.ToListAsync();
      }

and in the markup:

      <select asp-for="MovieGenre" asp-items="Model.Genres"/> ...
      <input type="text" asp-for="SearchString" />

Add a new field
---
I add a string Rating proerty to Models\Movie.cs: public string Rating { get; set; }

I have to update markup for all the pages that will display Rating -- this is somewhat repetitive, a Movie control would help. Also I do not like all of the literal strings in the markup that must tie to the code exactly.  This is fragile and not checked by the compiler and also does not have as much intellisense as I would like. 

I have to add a checkpoint to the DB: dotnet ef add-migration RatingField
I have to recreate the database witht the new schema from code: dotnet ef database update

Add validation
---
Update the Movie class to take advantage of the Required, StringLength, RegularExpression, and Range validation attributes.

Non-nullable value types (such as decimal, int, float, and DateTime) are inherently required and don't need the Required attribute.

Publishing
---
I tried to create a profile and publish but it failed.  There are errors like this

      C:\Program Files\dotnet\sdk\2.2.103\Sdks\Microsoft.NET.Sdk.Publish\build\netstandard1.0\PublishTargets\
      Microsoft.NET.Sdk.Publish.FileSystem.targets(42,5): 
      Error MSB3021: 
      Unable to copy file      
      "C:\projects\MVCWeb\RazorPagesMovie\RazorPagesMovie\obj\Release\netcoreapp2.2\PubTmp\Out\dotnet-aspnet-codegenerator-design.dll"
      to
      "http://localhost/RazorWebPages\dotnet-aspnet-codegenerator-design.dll". 
      **The given path's format is not supported.**

I had used a FileSystem Publish, but used a  url as the Target Location.  CHanging to a folder.
      C:\projects\MVCWeb\RazorPagesMoviePublish

This succeeded:

      ------ Publish started: Project: RazorPagesMovie, Configuration: Release Any CPU ------
      Connecting to C:\projects\MVCWeb\RazorPagesMoviePublish...
      RazorPagesMovie -> C:\projects\MVCWeb\RazorPagesMovie\RazorPagesMovie\bin\Release\netcoreapp2.2\RazorPagesMovie.dll
      RazorPagesMovie -> C:\projects\MVCWeb\RazorPagesMovie\RazorPagesMovie\bin\Release\netcoreapp2.2\RazorPagesMovie.Views.dll
      RazorPagesMovie -> C:\projects\MVCWeb\RazorPagesMovie\RazorPagesMovie\obj\Release\netcoreapp2.2\PubTmp\Out\
      Web App was published successfully file:///C:/projects/MVCWeb/RazorPagesMoviePublish

I ended up having to add a site in IIS
      port = 5000
      content path = C:/projects/MVCWeb/RazorPagesMoviePublish
      App Pool = No Managed code

I can how browser to localhost:5000 and see the site however I cannot query the DB.

First I had an issue that the site was raising exceptions and it did not want to display those in production.  In order to see teh exceptions in prod, I changed to web.config to say I was in Development.

      <aspNetCore processPath="dotnet" arguments=".\RazorPagesMovie.dll" stdoutLogEnabled="false" stdoutLogFile=".\logs\stdout" hostingModel="InProcess" >
        <environmentVariables>
            <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
         </environmentVariables>

Then I find the exception is regarding connecting to LocalDB from IIS.  

> SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections

> Event log: Cannot get a local application data path. Most probably a user profile is not loaded. If LocalDB is executed under IIS, make sure that profile loading is enabled for the current user.

LocalDB as I set it up is a profile-specific resource and it cannot be accessed from IIS be default.  It is possible to allow this web app to access IIS.

> As described in KB 2547655 enabling loadUserProfile is not enough to fully load user profile, we also need to enable setProfileEnvironment. This requires editing applicationHost.config file which is usually located in C:\Windows\System32\inetsrv\config. Following the instructions from KB 2547655 we should enable both flags for Application Pool ASP.NET v4.0.

      <add name="DotNetMVC" autoStart="true" managedRuntimeVersion="">
                <processModel identityType="ApplicationPoolIdentity" loadUserProfile="true" setProfileEnvironment="true"/>
      </add>

The site now runs using IIS and LocalDB.

However, it is not using the dev DB under the mark Profile.  Rather, it has created a new profile 
C:\Users\DotNetMVC with its own LocalDB.  

I find my profiles folder is a bit of a junkyard of old projects as it appears Windows/IIS/.NET have been happily setting these up over the years:

      C:\Users\
      03/04/2015  11:21 PM    <DIR>          TEMP
      03/04/2015  11:21 PM    <DIR>          TEMP.IIS APPPOOL
      04/15/2015  09:09 PM    <DIR>          AspTest
      04/15/2015  09:09 PM    <DIR>          testing
      09/07/2015  04:11 PM    <DIR>          Classic .NET AppPool
      05/27/2016  10:37 AM    <DIR>          gmWebSite
      05/28/2016  10:38 AM    <DIR>          dev0.gmi.local
      07/17/2016  02:10 PM    <DIR>          ASPClassicPool
      08/23/2016  02:14 PM    <DIR>          Public
      11/19/2016  09:24 AM    <DIR>          gmSpecASP
      01/02/2017  06:53 PM    <DIR>          dev.greatmigrations.com
      01/02/2017  07:55 PM    <DIR>          ASP.NET v4.0
      01/03/2017  12:47 PM    <DIR>          FMStocks
      03/03/2017  12:27 PM    <DIR>          iis.greatmigrations.com
      05/05/2017  11:44 AM    <DIR>          www.greatmigrations.com
      07/11/2017  09:48 AM    <DIR>          info.greatmigrations.com
      07/11/2017  11:35 AM    <DIR>          test.greatmigrations.com
      10/25/2017  01:24 PM    <DIR>          info
      02/05/2019  08:25 AM    <DIR>          DefaultAppPool
      02/05/2019  09:12 AM    <DIR>          DotNetMVC
      02/05/2019  09:21 AM    <DIR>          mark

[More on markdown](https://www.markdownguide.org/basic-syntax/)