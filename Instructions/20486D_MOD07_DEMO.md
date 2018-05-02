# Module 7: Using Entity Framework Core in ASP.NET Core

# Lesson 2: Working with Entity Framework Core

### Demonstration: How to Use Entity Framework Core

#### Preparation Steps 

1. Ensure that you have cloned the 20486D directory from GitHub. It contains the code segments for this course's labs and demos. 
 https://github.com/MicrosoftLearning/20486D-DevelopingASPNETMVCWebApplications/tree/master/Allfiles.

2. Go to **Allfiles\Mod07\Democode\01_EntityFrameworkExample_begin**, and then double-click **EntityFrameworkExample.sln**.

#### Demonstration Steps

1. In the Solution Explorer pane, under **EntityFrameworkExample**, expand **Data**, and then click **PersonContext.cs**.

2. In the **PersonContext.cs** code window, locate the following code.

  ```cs
      using System.Threading.Tasks;
```
3. Ensure that the mouse cursor is at the end of the  **System.Threading.Tasks** namespace, press Enter, and then type the following code.

  ```cs
      using EntityFrameworkExample.Models;
      using Microsoft.EntityFrameworkCore;    
```
4. In the **PersonContext.cs** code window, locate the following code.

  ```cs
      public class PersonContext
```
5.  Append the following code to the existing line of code.

  ```cs
      : DbContext
```

6. In the **PersonContext.cs** code block, press Enter, and then type the following code.


  ```cs
      public PersonContext(DbContextOptions<PersonContext> options) 
          :base(options)
      {
      }

      public DbSet<Person> People { get; set; }
```
 
7. In the Solution Explorer pane, under **EntityFrameworkExample**, under **Data**, click **DbInitializer.cs**.

8. In the **DbInitializer.cs** code window, locate the following code.

  ```cs
      using System.Threading.Tasks;
```
9. Ensure that the mouse cursor is at the end of the  **System.Threading.Tasks** namespace, press Enter, and then type the following code.

  ```cs
      using EntityFrameworkExample.Models;   
```
10. In the **DbInitializer.cs** code block, press Enter, and then type the following code.

  ```cs
      public static void Initialize(PersonContext context)
      {
          context.Database.EnsureCreated();
          if (context.People.Any())
          {
                return; 
          }

          var people = new Person[]
          {
          new Person { FirstName = "Tara", LastName = "Brewer", City = "Ocala", Address = "317 Long Street" },
          new Person { FirstName = "Andrew", LastName = "Tippett", City = "Anaheim", Address = "3163 Nickel Road" }
          };

          foreach (Person p in people)
          {
             context.People.Add(p);
          }
          context.SaveChanges();
      } 
```

11. In the Solution Explorer pane of the **EntityFrameworkExample - Microsoft Visual Studio** window, click **Startup.cs**.

12. In the **Startup.cs** code window, locate the following code.

  ```cs
      using Microsoft.Extensions.DependencyInjection;
```
13. Ensure that the mouse cursor is at the end of the  **Microsoft.Extensions.DependencyInjection** namespace, press Enter, and then type the following code.

  ```cs
      using Microsoft.Extensions.Configuration;
      using EntityFrameworkExample.Data;
      using Microsoft.EntityFrameworkCore; 
```

14. In the **Startup.cs** code window, locate the following code.

  ```cs
      public void ConfigureServices(IServiceCollection services)
```
15. Place the mouse cursor before the located code, type the following code, and then press Enter.

  ```cs
      private IConfiguration _configuration;

      public Startup(IConfiguration configuration)
      {
          _configuration = configuration;
      }
```

16. In the **ConfigureServices** method code block, locate the following code.

  ```cs
      services.AddMvc();
```

17. Place the mouse cursor before the located code, type the following code, and then press Enter.

  ```cs
      services.AddDbContext<PersonContext>(options =>
             options.UseInMemoryDatabase("PersonDB"));
```
18. In the Solution Explorer pane of the **EntityFrameworkExample - Microsoft Visual Studio** window, click **Program.cs**.

19. In the **Program.cs** code window, locate the following code.

  ```cs
      using Microsoft.Extensions.Logging;
```
20. Ensure that the mouse cursor is at the end of the  **Microsoft.Extensions.Logging** namespace, press Enter, and then type the following code.

  ```cs
      using Microsoft.Extensions.DependencyInjection;
      using EntityFrameworkExample.Data;
```

21. In the **Program.cs** code block, select the following code.

  ```cs
      BuildWebHost(args).Run();
```

22. Replace the selected code with the following code.

  ```cs
      var host = BuildWebHost(args);
      using (var scope = host.Services.CreateScope())
      {
          var services = scope.ServiceProvider;
          try
          {
              var context = services.GetRequiredService<PersonContext>();
              DbInitializer.Initialize(context);
          }
          catch (Exception ex)
          {
            //Handling an exception 
          }
      }
      host.Run();
```

23. In the Solution Explorer pane, under **EntityFrameworkExample**, expand **Controllers**, and then click **PersonController.cs**.

24. In the **PersonController.cs** code window, locate the following code.

  ```cs
      using Microsoft.AspNetCore.Mvc;
```
25. Ensure that the mouse cursor is at the end of the  **Microsoft.AspNetCore.Mvc** namespace, press Enter, and then type the following code.

  ```cs
      using EntityFrameworkExample.Data;
      using EntityFrameworkExample.Models;
```

26. In the **PersonController.cs** code window, locate the following code.

  ```cs
      public IActionResult Index()
```
27. Place the mouse cursor before the located code, type the following code, and then press Enter.

  ```cs
      private readonly PersonContext _context;

      public PersonController(PersonContext context)
      {
         _context = context;
      }

```

28. In the **PersonController.cs** code block, in the **Index** action code block, select the following code.

  ```cs
       return View();
```
29. Replace the selected code with the following code.

  ```cs
       return View(_context.People.ToList());
```
30. Ensure that the cursor is at the end of the **Index** action code block, press Enter twice, and then type the following code.

  ```cs
       public IActionResult Edit(int id)
       {
       }
```
31. In the **Edit** action code block, type the following code.

  ```cs
       var person = _context.People.SingleOrDefault(m => m.PersonId == id);
       person.FirstName = "Brandon";
       _context.Update(person);
       _context.SaveChanges();
       return RedirectToAction(nameof(Index));
```
32. Ensure that the cursor is at the end of the **Edit** action code block, press Enter twice, and then type the following code.

  ```cs
       public IActionResult Create()
       {
       }
```
33. In the **Create** action code block, type the following code.

  ```cs
       _context.Add(new Person() { FirstName = "Robert ", LastName = "Berends", City = "Birmingham", Address = "2632 Petunia Way" });
       _context.SaveChanges();
       return RedirectToAction(nameof(Index));
```

34. Ensure that the cursor is at the end of the **Create** action code block, press Enter twice, and then type the following code.

  ```cs
       public IActionResult Delete(int id)
       {
       }
```
35. In the **Delete** action code block, type the following code.

  ```cs
      var person = _context.People.SingleOrDefault(m => m.PersonId == id);
      _context.People.Remove(person);
      _context.SaveChanges();
      return RedirectToAction(nameof(Index));
```

36. On the **FILE** menu of the **EntityFrameworkExample - Microsoft Visual Studio** window, click **Save All**.

37. On the **DEBUG** menu of the **EntityFrameworkExample - Microsoft Visual Studio** window, click **Start Debugging**.

      >**Note:** The browser window displays the **Index.cshtml** page.

38. In the **Microsoft Edge** window, click **Create New Person**.

39. In the **Microsoft Edge** window, select a person of your choice, and click **Edit**.

40. In the **Microsoft Edge** window, select a person of your choice, and click **Delete**.

41. In the **Microsoft Edge** window, click **Close**.

42. On the **DEBUG** menu of the **EntityFrameworkExample (Running) - Microsoft Visual Studio** window, click **Stop Debugging**.

43. On the **FILE** menu of the **EntityFrameworkExample - Microsoft Visual Studio** window, click **Exit**.

# Lesson 3: Using Entity Framework Core to Connect to Microsoft SQL Server

### Demonstration: How to Apply the Repository Pattern

#### Preparation Steps 

1. Ensure that you have cloned the 20486D directory from GitHub. It contains the code segments for this course's labs and demos. 
 https://github.com/MicrosoftLearning/20486D-DevelopingASPNETMVCWebApplications/tree/master/Allfiles.

2. Go to **Allfiles\Mod07\Democode\02_RepositoryExample_begin**, and then double-click **EntityFrameworkExample.sln**.

#### Demonstration Steps

1. In the Solution Explorer pane, under **EntityFrameworkExample**, expand **Repositories**, and then click **IRepository.cs**.

2. In the **IRepository.cs** code window, locate the following code.

  ```cs
      using System.Threading.Tasks;
```
3. Ensure that the mouse cursor is at the end of the  **System.Threading.Tasks** namespace, press Enter, and then type the following code.

  ```cs
      using EntityFrameworkExample.Models;
```

4. In the **IRepository.cs** code block, press Enter, and then type the following code.

  ```cs
      IEnumerable<Person> GetPeople();
      void CreatePerson();
      void DeletePerson(int id);
      void UpdatePerson(int id);
```

5. In the Solution Explorer pane, under **EntityFrameworkExample**, under **Repositories**, click **MyRepository.cs**.

6. In the **MyRepository.cs** code window, locate the following code.

  ```cs
      using System.Threading.Tasks;
```
7. Ensure that the mouse cursor is at the end of the  **System.Threading.Tasks** namespace, press Enter, and then type the following code.

  ```cs
      using EntityFrameworkExample.Data;
      using EntityFrameworkExample.Models;
```
8. In the **MyRepository.cs** code window, locate the following code.

  ```cs
      public class MyRepository
```
9.  Append the following code to the existing line of code.

  ```cs
      : IRepository
```

10. In the **MyRepository.cs** code block, press Enter, and then type the following code.

  ```cs
      private PersonContext _context;

      public MyRepository(PersonContext context)
      {
            _context = context;
      }
```

11. Ensure that the cursor is at the end of the **MyRepository** method code block, press Enter twice, and then type the following code.

  ```cs
      public void CreatePerson()
      {
           _context.Add(new Person() { FirstName = "Robert ", LastName = "Berends", City = "Birmingham", Address = "2632 Petunia Way" });
           _context.SaveChanges();
      }
```
12. Ensure that the cursor is at the end of the **CreatePerson** method code block, press Enter twice, and then type the following code.

  ```cs
      public void DeletePerson(int id)
      {
          var person = _context.People.SingleOrDefault(m => m.PersonId == id);
          _context.People.Remove(person);
          _context.SaveChanges();
      }
```

13. Ensure that the cursor is at the end of the **DeletePerson** method code block, press Enter twice, and then type the following code.

  ```cs
      public IEnumerable<Person> GetPeople()
      {
           return _context.People.ToList();
      }
```

14. Ensure that the cursor is at the end of the **GetPeople** method code block, press Enter twice, and then type the following code.

  ```cs
      public void UpdatePerson(int id)
      {
           var person = _context.People.SingleOrDefault(m => m.PersonId == id);
           person.FirstName = "Brandon";
           _context.Update(person);
           _context.SaveChanges();
      }
```

15. In the Solution Explorer pane of the **EntityFrameworkExample - Microsoft Visual Studio** window, click **Startup.cs**.

16. In the **Startup.cs** code window, locate the following code.

  ```cs
      using Microsoft.Extensions.Configuration;
```
17. Ensure that the mouse cursor is at the end of the  **Microsoft.Extensions.Configuration** namespace, press Enter, and then type the following code.

  ```cs
      using EntityFrameworkExample.Data;
      using Microsoft.EntityFrameworkCore;
      using EntityFrameworkExample.Repositories;
```

18. In the **Startup.cs** code window, locate the following code.

  ```cs
      services.AddMvc();
```
19. Place the mouse cursor before the located code, type the following code, and then press Enter.

  ```cs
      services.AddDbContext<PersonContext>(options =>
                 options.UseSqlServer(_configuration.GetConnectionString("DefaultConnection")));

      services.AddScoped<IRepository, MyRepository>();
```

>**Note:** Verify in the Solution Explorer pane, under **EntityFrameworkExample**, the appsettings.json that contains the **DefaultConnection** string.

20. In the Solution Explorer pane, under **EntityFrameworkExample**, expand **Controllers**, and then click **PersonController.cs**.

21. In the **PersonController.cs** code window, locate the following code.

  ```cs
      using Microsoft.AspNetCore.Mvc;
```
22. Ensure that the mouse cursor is at the end of the  **Microsoft.AspNetCore.Mvc** namespace, press Enter, and then type the following code.

  ```cs
      using EntityFrameworkExample.Repositories;
```

23. In the **PersonController.cs** code window, locate the following code.

  ```cs
      public IActionResult Index()
```
24. Place the mouse cursor before the located code, type the following code, and then press Enter.

  ```cs
      private IRepository _repository;

      public PersonController(IRepository repository)
      {
           _repository = repository;
      }

```

25. In the **PersonController.cs** code block, in the **Index** action code block, select the following code.

  ```cs
       return View();
```
26. Replace the selected code with the following code.

  ```cs
       var list = _repository.GetPeople();
       return View(list);
```
27. Ensure that the cursor is at the end of the **Index** action code block, press Enter twice, and then type the following code.

  ```cs
       public IActionResult Edit(int id)
       {
       }
```
28. In the **Edit** action code block, type the following code.

  ```cs
       _repository.UpdatePerson(id);
       return RedirectToAction(nameof(Index));
```
29. Ensure that the cursor is at the end of the **Edit** action code block, press Enter twice, and then type the following code.

  ```cs
       public IActionResult Create()
       {
       }
```
30. In the **Create** action code block, type the following code.

  ```cs
       _repository.CreatePerson();
       return RedirectToAction(nameof(Index));
```

31. Ensure that the cursor is at the end of the **Create** action code block, press Enter twice, and then type the following code.

  ```cs
       public IActionResult Delete(int id)
       {
       }
```
32. In the **Delete** action code block, type the following code.

  ```cs
      _repository.DeletePerson(id);
      return RedirectToAction(nameof(Index));
```

33. On the **FILE** menu of the **EntityFrameworkExample - Microsoft Visual Studio** window, click **Save All**.

34. On the **DEBUG** menu of the **EntityFrameworkExample - Microsoft Visual Studio** window, click **Start Debugging**.

      >**Note:** The browser window displays the **Index.cshtml** page.

35. In the **Microsoft Edge** window, click **Create New Person**.

36. In the **Microsoft Edge** window, select a person of your choice, and click **Edit**.

37. In the **Microsoft Edge** window, select a person of your choice, and click **Delete**.

38. In the **Microsoft Edge** window, click **Close**.

39. On the **DEBUG** menu of the **EntityFrameworkExample (Running) - Microsoft Visual Studio** window, click **Stop Debugging**.

40. On the **FILE** menu of the **EntityFrameworkExample - Microsoft Visual Studio** window, click **Exit**.

©2016 Microsoft Corporation. All rights reserved.

The text in this document is available under the  [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are  **not**  included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided &quot;as-is.&quot; Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.