# EntityFramework-Code-First-With-SqlServer-Compact-Reference
 EntityFramework Code First With SqlServer Compact Reference
 
 
 
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
 
namespace sqlcesample
{
    /*
     * packages
 
        Install-Package System.Data.SQLite
        Install-Package EntityFramework.SqlServerCompact
 
        <?xml version="1.0" encoding="utf-8"?>
        <packages>
        <package id="EntityFramework" version="6.1.3" targetFramework="net452" />
        <package id="EntityFramework.SqlServerCompact" version="6.1.3" targetFramework="net452" />
        <package id="Microsoft.SqlServer.Compact" version="4.0.8876.1" targetFramework="net452" />
        <package id="System.Data.SQLite" version="1.0.101.0" targetFramework="net452" />
        <package id="System.Data.SQLite.Core" version="1.0.101.0" targetFramework="net452" />
        <package id="System.Data.SQLite.EF6" version="1.0.101.0" targetFramework="net452" />
        <package id="System.Data.SQLite.Linq" version="1.0.101.0" targetFramework="net452" />
        </packages>
 
     */
 
    [TestClass]
    public class UnitTest1
    {
        [TestMethod]
        public void TestMethod1()
        {
            /*
             Enable-Migrations -Force
             Add-Migration init
             Update-Database
 
             – After the command is executed, a new folder Migrations will be created in project. This folder contains a _InitialCreate.cs and a Configuration.cs where
             + _InitialCreate.cs : is the first migration, it contains the changes that have already been applied to the database from an empty database to one that includes the Courses and Students tables. Although Code First already creates automatically the tables for us, the steps to create them have also been converted into a migration. However Code First has marked in database that this migration has already been applied. Therefore the application won’t try to create table again. The time stamp before file name is used for ordering purposes.
             + Configuration.cs : contains settings that migrations will use for migrating
 
             Database.SetInitializer(new MigrateDatabaseToLatestVersion<CourseraContext, Configuration>());
             */
 
            /*
             – The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any change found.
             AddUrlToCourse is the name of migration. After the command is executed, a new migration class with time stamp will be created.
 
             Add-Migration AddUrlToCourse
             */
 
            /*
             – Although the migration was created, we need to explicit update database by calling Update-Database in Package Manager Console
               Update-Database
             */
 
            /*
             – Although the migration was created, we need to explicit update database by calling Update-Database in Package Manager Console
             – This command will apply any pending migrations to the database
             – This command also supports parameters such as Verbose, Script or TagetMigration
            Update-Database
             */
 
            /*
 
             + TargetMigration: specifies to which migration we would like to bring database schema to.
             If you want to reset to empty database, you can run the command
 
             Update-Database –TargetMigration: $InitialDatabase
             */
 
            /*
             – If you want to always update database schema to latest migration when the application starts, use this code listing at program’s start-up.
              Database.SetInitializer(new MigrateDatabaseToLatestVersion<CourseraContext, Configuration>());
             */
            //  Database.SetInitializer(new MigrateDatabaseToLatestVersion<CourseraContext, Configuration>());
            const string name = "wooooo";
            using (var db = new CourseraContext())
            {
                var course = new Course { Name = name };
                db.Courses.Add(course);
                db.SaveChanges();
            }
            using (var db = new CourseraContext())
            {
                var t = db.Courses.FirstOrDefault();
                Console.WriteLine(t);
            }
        }
 
        internal class Course
        {
            public int Id { get; set; }
 
            public string Name { get; set; }
 
            public string Url { get; set; }
 
            public virtual List<Student> Students { get; set; }
        }
 
        internal class Student
        {
            public int Id { get; set; }
 
            public string FirstName { get; set; }
 
            public string LastName { get; set; }
 
            public int CourseId { get; set; }
 
            public virtual Course Course { get; set; }
        }
 
        /*
 
         this class is auto generated when you do enablemigration
 
        internal sealed class Configuration : DbMigrationsConfiguration<sqlcesample.CourseraContext>
 
        internal sealed class Configuration : DbMigrationsConfiguration<sqlcesample.CourseraContext>
        {
            public Configuration()
            {
                AutomaticMigrationsEnabled = false;
                ContextKey = "sqlcesample.CourseraContext";
            }
 
            protected override void Seed(sqlcesample.CourseraContext context)
            {
                //  This method will be called after migrating to the latest version.
 
                //  You can use the DbSet<T>.AddOrUpdate() helper extension method
                //  to avoid creating duplicate seed data. E.g.
                //
                //    context.People.AddOrUpdate(
                //      p => p.FullName,
                //      new Person { FullName = "Andrew Peters" },
                //      new Person { FullName = "Brice Lambson" },
                //      new Person { FullName = "Rowan Miller" }
                //    );
                //
            }
        }
        */
 
        internal class CourseraContext : DbContext
        {
            public CourseraContext()
            //optional
            //: base("name=DefaultConnection")
            {
            }
 
            public DbSet<Course> Courses { get; set; }
            public DbSet<Student> Students { get; set; }
            /*
             To access the fluent API, you override the OnModelCreating method in DbContext. For example,
             we want to rename the column UserName of Teacher table into “Id”.
            Override the OnModelCreating method on with the following code
 
             protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                modelBuilder.Entity<Student>()
                    .Property(u => u.Id)
                    .HasColumnName("Idmooo");
            }
            */
        }
    }
}








Update-Database -TargetMigration:0 -force [This will destroy all tables and all data.]

using (var context = new PersonDbContext())
{
    var query = context.Persons.Where(x => x.Age == 34);
    Console.WriteLine(query.ToString());
}
Will output

SELECT 
[Extent1].[Id] AS [Id], 
[Extent1].[FirstName] AS [FirstName], 
[Extent1].[LastName] AS [LastName], 
[Extent1].[Age] AS [Age]
FROM [dbo].[People] AS [Extent1]
WHERE 34 = [Extent1].[Age]


How to: Execute Business Logic When Saving Changes





You can override the SaveChanges() method for your DbContext, as described at How to: Execute Business Logic When Saving Changes on MSDN.


public override int SaveChanges(SaveOptions options)
{
    //perform logic here
    return base.SaveChanges(options);
}

public override int SaveChanges(SaveOptions options)
{
  
    foreach (ObjectStateEntry entry in
        ObjectStateManager.GetObjectStateEntries(
        EntityState.Added | EntityState.Modified))
    {
        // Validate the objects in the Added and Modified state
        // if the validation fails throw an exeption.
    }
    return base.SaveChanges(options);
}







