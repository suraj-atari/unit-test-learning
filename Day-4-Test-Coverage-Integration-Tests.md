# Day 4: Test Coverage, Integration Tests & Best Practices

**Duration:** 4-6 hours
**Prerequisites:** Days 1-3 completed

---

## Learning Objectives

By the end of Day 4, you will:
- Understand different types of code coverage
- Measure and interpret coverage reports
- Know what to test (and what NOT to test)
- Write testable code following SOLID principles
- Create integration tests using in-memory databases
- Use the TestBase pattern effectively
- Distinguish between unit and integration tests

---

## Morning Session (2-3 hours)

### 4.1 Understanding Test Coverage

#### What is Code Coverage?

Code coverage measures what percentage of your code is executed when tests run.

**Formula:**
```
Coverage % = (Lines Executed / Total Lines) × 100
```

**Example:**
```csharp
public bool IsValid(string input)
{
    if (input == null) return false;       // Line 1
    if (input.Length == 0) return false;   // Line 2
    return true;                           // Line 3
}

// This test achieves 66% line coverage
[Fact]
public void IsValid_WithNull_ReturnsFalse()
{
    var result = IsValid(null);
    result.Should().BeFalse();
}
// Lines executed: 1, 2
// Lines NOT executed: 3
```

#### Why Coverage Matters

**Benefits:**
- Identifies untested code
- Helps find missing test scenarios
- Gives confidence in test suite
- Prevents regressions

**Limitations:**
- 100% coverage ≠ perfect tests
- Can have false sense of security
- Doesn't measure assertion quality
- Doesn't guarantee all scenarios tested

---

### 4.2 Types of Coverage

#### 1. Line Coverage (Most Common)

Percentage of code lines executed:

```csharp
public string GetStatus(User user)
{
    if (user == null)          // Line 1
        return "Invalid";      // Line 2

    if (user.Active)           // Line 3
        return "Active";       // Line 4

    return "Inactive";         // Line 5
}

// Test 1: null user
[Fact]
public void GetStatus_NullUser_ReturnsInvalid()
{
    var result = GetStatus(null);
    // Lines executed: 1, 2
    // Line coverage: 40%
}

// Test 2: active user
[Fact]
public void GetStatus_ActiveUser_ReturnsActive()
{
    var user = new User { Active = true };
    var result = GetStatus(user);
    // Lines executed: 1, 3, 4
    // Line coverage: 60%
}

// Test 3: inactive user
[Fact]
public void GetStatus_InactiveUser_ReturnsInactive()
{
    var user = new User { Active = false };
    var result = GetStatus(user);
    // Lines executed: 1, 3, 5
    // Line coverage: 60%
}

// All 3 tests together: 100% line coverage
```

#### 2. Branch Coverage (More Thorough)

Percentage of decision points (if/else) executed:

```csharp
public string GetDiscount(int age, bool isStudent)
{
    if (age < 18)              // Branch Point 1
        return "Child";
    else if (isStudent)        // Branch Point 2
        return "Student";
    else
        return "Adult";
}

// For 100% branch coverage, need to test:
// 1. age < 18 (true)
// 2. age >= 18 AND isStudent (true)
// 3. age >= 18 AND isStudent (false)

[Theory]
[InlineData(10, false, "Child")]      // Branch 1: true
[InlineData(25, true, "Student")]     // Branch 1: false, Branch 2: true
[InlineData(30, false, "Adult")]      // Branch 1: false, Branch 2: false
public void GetDiscount_AllBranches_ReturnsCorrectDiscount(
    int age, bool isStudent, string expected)
{
    var result = GetDiscount(age, isStudent);
    result.Should().Be(expected);
}
```

#### 3. Method Coverage

Percentage of methods called:

```csharp
public class Calculator
{
    public int Add(int a, int b) => a + b;        // Method 1
    public int Subtract(int a, int b) => a - b;   // Method 2
    public int Multiply(int a, int b) => a * b;   // Method 3
    public int Divide(int a, int b) => a / b;     // Method 4
}

// Testing only Add and Multiply
[Fact]
public void Add_Works() { /* ... */ }

[Fact]
public void Multiply_Works() { /* ... */ }

// Method coverage: 50% (2 out of 4 methods tested)
```

---

### 4.3 Coverage Goals

#### Industry Standards

| Coverage Level | Grade | Meaning |
|----------------|-------|---------|
| **< 50%** | Poor | Many untested scenarios |
| **50-70%** | Fair | Basic coverage |
| **70-80%** | Good | Solid coverage |
| **80-90%** | Excellent | Comprehensive testing |
| **90-100%** | Outstanding | Very thorough (may be overkill) |

#### Realistic Goals

**For Fujiq.Test:**
- **Target:** 70-80% overall coverage
- **Critical paths:** 90%+ coverage
- **Business logic:** 85%+ coverage
- **Simple DTOs/models:** 50-60% is fine

**Important:** Quality > Quantity
- Better to have 70% with good tests
- Than 100% with meaningless tests

---

### 4.4 Running Coverage in Fujiq.Test

#### Command Line (coverlet)

```bash
# Basic coverage
dotnet test /p:CollectCoverage=true

# With detailed output
dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover

# Exclude files from coverage
dotnet test /p:CollectCoverage=true /p:Exclude="[Fujiq.Test]*"
```

#### Generate HTML Report

```bash
# Install ReportGenerator (one-time)
dotnet tool install -g dotnet-reportgenerator-globaltool

# Run tests with coverage
dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover

# Generate HTML report
reportgenerator -reports:coverage.opencover.xml -targetdir:coveragereport -reporttypes:Html

# Open report (Windows)
start coveragereport/index.html

# Open report (Mac)
open coveragereport/index.html
```

#### Visual Studio

1. **Test Explorer** → Right-click → **Analyze Code Coverage**
2. View results in **Code Coverage Results** window
3. See highlighted lines in code (green = covered, red = not covered)

---

### 4.5 What to Test (and What NOT to Test)

#### DO Test

**1. Business Logic**
```csharp
// YES - Test this
public decimal CalculateDiscount(Order order)
{
    if (order.Total > 1000)
        return order.Total * 0.15m;
    else if (order.Total > 500)
        return order.Total * 0.10m;
    else
        return 0;
}
```

**2. Validation Logic**
```csharp
// YES - Test this
public bool IsValidEmail(string email)
{
    if (string.IsNullOrEmpty(email))
        return false;

    return email.Contains("@") && email.Contains(".");
}
```

**3. Conditional Branches**
```csharp
// YES - Test all branches
public string GetUserRole(User user)
{
    if (user.IsAdmin)
        return "Admin";
    else if (user.IsModerator)
        return "Moderator";
    else
        return "User";
}
```

**4. Edge Cases and Boundaries**
```csharp
// YES - Test edge cases
[Theory]
[InlineData(0)]      // Boundary: zero
[InlineData(1)]      // Boundary: minimum positive
[InlineData(-1)]     // Boundary: negative
[InlineData(int.MaxValue)]  // Boundary: maximum
public void Divide_EdgeCases(int value) { /* ... */ }
```

**5. Error Handling**
```csharp
// YES - Test exception scenarios
[Fact]
public async Task Delete_ShouldThrow_WhenUserNotFound()
{
    // Test that exceptions are thrown correctly
}
```

#### DON'T Test

**1. Simple Properties (Getters/Setters)**
```csharp
// NO - Don't test this
public class User
{
    public string Name { get; set; }  // No logic, no test needed
    public string Email { get; set; }
}
```

**2. Constructor Property Assignments**
```csharp
// NO - Don't test this
public class UserService
{
    private readonly IRepository _repo;

    public UserService(IRepository repo)
    {
        _repo = repo;  // Simple assignment, no test needed
    }
}
```

**3. Framework Code**
```csharp
// NO - Don't test Entity Framework, ASP.NET, etc.
public class FujiqDbContext : DbContext
{
    public DbSet<User> Users { get; set; }  // Framework code
}
```

**4. Third-Party Libraries**
```csharp
// NO - Don't test libraries (they have their own tests)
var json = JsonConvert.SerializeObject(obj);  // Newtonsoft is already tested
```

**5. Private Methods (Directly)**
```csharp
// NO - Test private methods indirectly through public methods
private bool IsValidInternal(string value)
{
    // Test this via public methods that call it
}
```

---

### 4.6 Writing Testable Code

#### Principle 1: Dependency Injection

**Bad (Hard to Test):**
```csharp
public class UserService
{
    private readonly UserRepository _repo = new UserRepository();
    // ❌ Can't mock the repository
}
```

**Good (Easy to Test):**
```csharp
public class UserService
{
    private readonly IUserRepository _repo;

    public UserService(IUserRepository repo)
    {
        _repo = repo;  // ✅ Can inject a mock
    }
}
```

#### Principle 2: Single Responsibility

**Bad (Multiple Responsibilities):**
```csharp
public class UserService
{
    public void CreateUser(User user)
    {
        // Validate
        if (user.Email == null) throw new Exception();

        // Save to database
        var sql = "INSERT INTO Users...";
        _connection.Execute(sql);

        // Send email
        var smtp = new SmtpClient();
        smtp.Send(user.Email, "Welcome");

        // Log
        File.AppendAllText("log.txt", "User created");
    }
}
```

**Good (Separated Responsibilities):**
```csharp
public class UserService
{
    private readonly IUserRepository _repo;
    private readonly IEmailService _email;
    private readonly ILogger _logger;

    public void CreateUser(User user)
    {
        if (user.Email == null)
            throw new ArgumentException();

        _repo.Add(user);
        _email.SendWelcome(user.Email);
        _logger.LogInformation($"User {user.Id} created");
    }
}
```

#### Principle 3: Avoid Static Dependencies

**Bad (Can't Mock):**
```csharp
public class OrderService
{
    public bool IsExpired(Order order)
    {
        return order.ExpiryDate < DateTime.Now;  // ❌ Can't mock DateTime.Now
    }
}
```

**Good (Testable):**
```csharp
public interface ITimeProvider
{
    DateTime Now { get; }
}

public class OrderService
{
    private readonly ITimeProvider _timeProvider;

    public bool IsExpired(Order order)
    {
        return order.ExpiryDate < _timeProvider.Now;  // ✅ Can mock
    }
}
```

#### Principle 4: Small, Focused Methods

**Bad (Too Complex):**
```csharp
public void ProcessOrder(Order order)
{
    // 200 lines of complex logic
    // Hard to test all scenarios
}
```

**Good (Decomposed):**
```csharp
public void ProcessOrder(Order order)
{
    ValidateOrder(order);
    ApplyDiscounts(order);
    CalculateTax(order);
    SaveOrder(order);
    SendConfirmation(order);
}

// Each method is small and testable
private void ValidateOrder(Order order) { /* ... */ }
private void ApplyDiscounts(Order order) { /* ... */ }
```

---

## Afternoon Session (2-3 hours)

### 4.7 Integration Tests

#### What is an Integration Test?

Tests that verify **multiple components** work together:
- Database interactions
- API endpoints
- External services
- Complete workflows
- Real Entity Framework behavior

#### Unit Test vs Integration Test

| Aspect | Unit Test | Integration Test |
|--------|-----------|------------------|
| **Dependencies** | Mocked | Real or In-Memory |
| **Speed** | Fast (milliseconds) | Slower (seconds) |
| **Scope** | Single method/class | Multiple components |
| **Database** | No database | In-memory or test DB |
| **Isolation** | Complete | Partial |
| **Purpose** | Test logic | Test integration |
| **Quantity** | Many | Fewer |

**Example Comparison:**

```csharp
// UNIT TEST - Mocked repository
[Fact]
public async Task GetUser_UnitTest()
{
    // Arrange
    var mockRepo = new Mock<IRepository<User>>();
    mockRepo.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
        .ReturnsAsync(new User { Name = "John" });

    var service = new UserService(mockRepo.Object);

    // Act
    var result = await service.GetUserAsync(Guid.NewGuid());

    // Assert
    result.Name.Should().Be("John");
}

// INTEGRATION TEST - Real in-memory database
[Fact]
public async Task GetUser_IntegrationTest()
{
    // Arrange
    using var context = GetInMemoryDbContext();

    var user = new User { Name = "John" };
    context.Users.Add(user);
    await context.SaveChangesAsync();

    var service = new UserService(context);

    // Act
    var result = await service.GetUserAsync(user.Id);

    // Assert
    result.Name.Should().Be("John");
}
```

---

### 4.8 Integration Tests in Fujiq.Test

#### Example from FujiqDomainIntegrationTests

**File:** `Integration/FujiqDomainIntegrationTests.cs`

```csharp
public class FujiqDomainIntegrationTests : TestBase
{
    [Fact]
    public void Should_Save_And_Retrieve_Complete_Gaming_Ecosystem()
    {
        // Arrange - Use in-memory database
        using var context = GetInMemoryDbContext();

        var client = MockDataBuilders.ClientBuilder.Create(
            name: "Epic Games Studio"
        );

        var game = MockDataBuilders.GamesMasterBuilder.Create(
            name: "Fortnite Battle Royale",
            steamAppId: "578080",
            clientId: client.Id
        );

        var steamSales = MockDataBuilders.SteamBuilder.CreateList(7, game.Id);
        var xboxSales = MockDataBuilders.XboxBuilder.CreateList(7, game.Id);

        // Act - Save to database (tests EF relationships)
        context.Clients.Add(client);
        context.GamesMasters.Add(game);
        context.Steams.AddRange(steamSales);
        context.Xboxes.AddRange(xboxSales);
        context.SaveChanges();

        // Assert - Retrieve and verify (tests EF Include/navigation)
        var savedGame = context.GamesMasters
            .Include(g => g.Client)
            .Include(g => g.Steams)
            .Include(g => g.Xboxes)
            .First(g => g.Id == game.Id);

        savedGame.Should().NotBeNull();
        savedGame.Name.Should().Be("Fortnite Battle Royale");
        savedGame.Client.Name.Should().Be("Epic Games Studio");
        savedGame.Steams.Should().HaveCount(7);
        savedGame.Xboxes.Should().HaveCount(7);
    }
}
```

**What This Tests:**
- Entity Framework relationships
- Navigation properties
- Include/ThenInclude
- SaveChanges behavior
- Data persistence
- Complete workflows

---

### 4.9 TestBase Pattern

**File:** `Common/TestBase.cs`

```csharp
public abstract class TestBase : IDisposable
{
    protected readonly IServiceProvider ServiceProvider;
    protected readonly Mock<ILogger<object>> MockLogger;
    protected readonly Mock<IConfiguration> MockConfiguration;

    protected TestBase()
    {
        MockLogger = MockHelpers.CreateLogger<object>();
        MockConfiguration = new Mock<IConfiguration>();
    }

    /// <summary>
    /// Creates an in-memory database for integration testing
    /// </summary>
    protected FujiqDbContext GetInMemoryDbContext()
    {
        var options = new DbContextOptionsBuilder<FujiqDbContext>()
            .UseInMemoryDatabase(databaseName: Guid.NewGuid().ToString())
            .Options;

        return new FujiqDbContext(options);
    }

    public void Dispose()
    {
        // Cleanup resources
        GC.SuppressFinalize(this);
    }
}
```

**Benefits:**
- Common setup for all integration tests
- In-memory database with unique name per test (isolation)
- Shared mock helpers
- Proper cleanup with IDisposable

**Usage:**
```csharp
public class MyIntegrationTests : TestBase
{
    [Fact]
    public async Task My_Test()
    {
        // Automatically have access to:
        // - GetInMemoryDbContext()
        // - MockLogger
        // - MockConfiguration
    }
}
```

---

### 4.10 Hands-On Exercises

#### Exercise 4.1: Run Coverage

```bash
# Navigate to Fujiq.Test
cd Fujiq.Test

# Run tests with coverage
dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover

# Install report generator
dotnet tool install -g dotnet-reportgenerator-globaltool

# Generate HTML report
reportgenerator -reports:coverage.opencover.xml -targetdir:coveragereport -reporttypes:Html

# Open report
start coveragereport/index.html
```

**Tasks:**
1. Review overall coverage percentage
2. Identify files with low coverage
3. Identify untested branches
4. Find critical code that needs tests

#### Exercise 4.2: Write Integration Test

Create: `Exercises/Day4_IntegrationTests.cs`

```csharp
using Fujiq.Test.Common;
using Microsoft.EntityFrameworkCore;
using Xunit;
using FluentAssertions;

namespace Fujiq.Test.Exercises
{
    public class Day4_IntegrationTests : TestBase
    {
        [Fact]
        public async Task Should_Create_And_Retrieve_User_From_Database()
        {
            // Arrange
            using var context = GetInMemoryDbContext();

            var user = MockDataBuilders.ApplicationUserBuilder.Create(
                username: "testuser",
                firstName: "Test",
                lastName: "User"
            );

            // Act - Save to database
            context.ApplicationUsers.Add(user);
            await context.SaveChangesAsync();

            // Assert - Retrieve and verify
            var savedUser = await context.ApplicationUsers
                .FirstOrDefaultAsync(u => u.Username == "testuser");

            savedUser.Should().NotBeNull();
            savedUser!.FirstName.Should().Be("Test");
            savedUser.LastName.Should().Be("User");
        }

        [Fact]
        public async Task Should_Save_Game_With_Client_Relationship()
        {
            // Arrange
            using var context = GetInMemoryDbContext();

            var client = MockDataBuilders.ClientBuilder.Create(
                name: "Test Studio"
            );

            var game = MockDataBuilders.GamesMasterBuilder.Create(
                name: "Test Game",
                clientId: client.Id
            );

            // Act
            context.Clients.Add(client);
            context.GamesMasters.Add(game);
            await context.SaveChangesAsync();

            // Assert - Test navigation property
            var savedGame = await context.GamesMasters
                .Include(g => g.Client)
                .FirstAsync(g => g.Id == game.Id);

            savedGame.Client.Should().NotBeNull();
            savedGame.Client.Name.Should().Be("Test Studio");
        }

        [Fact]
        public async Task Should_Save_Game_With_Sales_Data()
        {
            // Arrange
            using var context = GetInMemoryDbContext();

            var game = MockDataBuilders.GamesMasterBuilder.Create(
                name: "Test Game"
            );

            var steamSales = MockDataBuilders.SteamBuilder.CreateList(5, game.Id);

            // Act
            context.GamesMasters.Add(game);
            context.Steams.AddRange(steamSales);
            await context.SaveChangesAsync();

            // Assert
            var savedGame = await context.GamesMasters
                .Include(g => g.Steams)
                .FirstAsync(g => g.Id == game.Id);

            savedGame.Steams.Should().HaveCount(5);
            savedGame.Steams.Should().AllSatisfy(s =>
            {
                s.GamesMasterId.Should().Be(game.Id);
                s.Revenue.Should().BeGreaterThan(0);
            });
        }
    }
}
```

---

## End of Day 4 - Assessment

### Self-Check Questions

1. **What's the difference between line coverage and branch coverage?**
   - Answer: Line coverage measures executed lines; branch coverage measures executed decision paths

2. **Should you aim for 100% code coverage? Why or why not?**
   - Answer: No, 70-80% is good. Focus on quality tests for critical code, not coverage metrics

3. **What's the difference between unit tests and integration tests?**
   - Answer: Unit tests mock dependencies and test single units; integration tests use real components

4. **When should you use in-memory database vs. mocks?**
   - Answer: Integration tests use in-memory DB; unit tests use mocks

5. **What should you NOT test?**
   - Answer: Simple properties, framework code, third-party libraries, private methods directly

### Practical Exercises

#### Exercise 1: Coverage Analysis
1. Run coverage on Fujiq.Test
2. Identify 3 files with low coverage
3. Write tests to improve coverage

#### Exercise 2: Integration Tests
1. Write 2 integration tests using in-memory DB
2. Test entity relationships
3. Test Include/navigation properties

#### Exercise 3: Testable Code Review
1. Find 3 examples of testable code in Fujiq
2. Find 3 examples of hard-to-test code
3. Suggest improvements

---

### Homework

#### Reading
1. **Study:** `Integration/FujiqDomainIntegrationTests.cs`
2. **Study:** `Common/TestBase.cs`
3. **Review:** Coverage report from Exercise 4.1

#### Practice
1. Write 2 more integration tests
2. Improve coverage in 1 low-coverage file
3. Refactor 1 hard-to-test method

---

### Key Takeaways

- Coverage is a useful metric but not the only goal
- 70-80% coverage is good; focus on quality over quantity
- Test business logic, validation, branches, edge cases
- Don't test properties, framework code, or third-party libraries
- Unit tests = mocked dependencies, Integration tests = real components
- Use TestBase for common integration test setup
- In-memory databases are perfect for integration testing

---

**Next:** [Day 5: Advanced Patterns & Real-World Practice](./Day-5-Advanced-Patterns-Practice.md)
