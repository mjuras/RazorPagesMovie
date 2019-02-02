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

There is one redord in the __EFMigrationsHistory

Scaffold (generate) Razor pages
---

Work with a database
---
Update Razor pages
---
Add search
---
Add a new field
---
Add validation
---


[More on markdown](https://www.markdownguide.org/basic-syntax/)