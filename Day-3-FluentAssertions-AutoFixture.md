# Day 3: FluentAssertions, AutoFixture & Test Data

**Duration:** 4-6 hours
**Prerequisites:** Day 1 & 2 completed, understanding of mocking

---

## Learning Objectives

By the end of Day 3, you will:
- Write readable assertions using FluentAssertions
- Generate test data automatically with AutoFixture
- Customize AutoFixture-generated objects
- Use Fujiq.Test's MockDataBuilders effectively
- Create complete test scenarios with realistic data
- Understand different test data strategies

---

## Morning Session (2-3 hours)

### 3.1 FluentAssertions - Readable Assertions

#### The Problem with Traditional Assertions

**Traditional xUnit:**
```csharp
Assert.NotNull(result);
Assert.Equal(expected, result);
Assert.True(result.IsActive);
Assert.Contains(expectedItem, collection);
```

**Problems:**
- Parameter order is confusing (expected vs actual)
- Not very readable
- Limited error messages
- Hard to chain multiple assertions

#### FluentAssertions Solution

```csharp
result.Should().NotBeNull();
result.Should().Be(expected);
result.IsActive.Should().BeTrue();
collection.Should().Contain(expectedItem);
```

**Benefits:**
- Natural language syntax
- Clear and readable
- Better error messages
- Chainable assertions
- IntelliSense-friendly

---

### 3.2 FluentAssertions Basic Patterns

#### Equality Assertions

```csharp
// Basic equality
result.Should().Be(expected);
result.Should().NotBe(unexpected);

// Null checks
result.Should().BeNull();
result.Should().NotBeNull();

// Reference equality
actual.Should().BeSameAs(expected);
actual.Should().NotBeSameAs(other);
```

#### Boolean Assertions

```csharp
isValid.Should().BeTrue();
isValid.Should().BeFalse();

// With reason
isValid.Should().BeTrue(because: "the user provided all required fields");
```

#### Numeric Assertions

```csharp
// Exact value
count.Should().Be(5);

// Comparisons
count.Should().BeGreaterThan(0);
count.Should().BeLessThan(100);
count.Should().BeGreaterOrEqualTo(10);
count.Should().BeLessOrEqualTo(50);

// Range
count.Should().BeInRange(1, 10);

// Approximation (useful for decimals/doubles)
price.Should().BeApproximately(99.99m, 0.01m);
```

---

### 3.3 FluentAssertions String Assertions

```csharp
// Exact match
name.Should().Be("John Doe");

// Contains
name.Should().Contain("John");
name.Should().NotContain("Admin");

// Start/End
name.Should().StartWith("John");
name.Should().EndWith("Doe");

// Case-insensitive
email.Should().Be("JOHN@EXAMPLE.COM", "case insensitive");

// Pattern matching
email.Should().Match("*@example.com");
email.Should().MatchRegex(@"^\w+@\w+\.\w+$");

// Empty/Null checks
value.Should().NotBeNullOrEmpty();
value.Should().NotBeNullOrWhiteSpace();

// Length
password.Should().HaveLength(8);
password.Should().HaveLengthGreaterThan(6);
```

---

### 3.4 FluentAssertions Collection Assertions

```csharp
var users = new List<User>
{
    new User { Name = "John", Active = true },
    new User { Name = "Jane", Active = true },
    new User { Name = "Bob", Active = false }
};

// Count
users.Should().NotBeNull();
users.Should().HaveCount(3);
users.Should().NotBeEmpty();
users.Should().HaveCountGreaterThan(2);

// Contains
users.Should().Contain(u => u.Name == "John");
users.Should().NotContain(u => u.Name == "Admin");

// Single item
var activeJohns = users.Where(u => u.Name == "John" && u.Active);
activeJohns.Should().ContainSingle();

// All items satisfy condition
var activeUsers = users.Where(u => u.Active);
activeUsers.Should().AllSatisfy(u =>
{
    u.Active.Should().BeTrue();
    u.Name.Should().NotBeNullOrEmpty();
});

// Ordering
var sortedNames = users.OrderBy(u => u.Name).Select(u => u.Name);
sortedNames.Should().BeInAscendingOrder();

// Equivalency (ignore order)
actualList.Should().BeEquivalentTo(expectedList);
```

---

### 3.5 FluentAssertions Object Assertions

```csharp
var user = new User
{
    Id = Guid.NewGuid(),
    Name = "John Doe",
    Email = "john@example.com",
    Active = true
};

// Property checks
user.Should().NotBeNull();
user.Id.Should().NotBeEmpty();
user.Name.Should().Be("John Doe");
user.Email.Should().Contain("@");
user.Active.Should().BeTrue();

// Check multiple properties at once
user.Should().BeEquivalentTo(new
{
    Name = "John Doe",
    Email = "john@example.com",
    Active = true
});

// Exclude properties from comparison
actualUser.Should().BeEquivalentTo(expectedUser, options => options
    .Excluding(u => u.Id)
    .Excluding(u => u.CreatedDate));

// Check property existence
user.Should().NotBeNull().And.Subject.Name.Should().NotBeNull();
```

---

### 3.6 FluentAssertions Exception Assertions

```csharp
// Synchronous methods
Action act = () => service.DeleteUser(Guid.Empty);

act.Should().Throw<ArgumentException>();

act.Should().Throw<ArgumentException>()
    .WithMessage("User ID cannot be empty");

act.Should().Throw<ArgumentException>()
    .WithMessage("*cannot be empty*")  // Wildcards
    .And.ParamName.Should().Be("userId");

// Async methods
Func<Task> act = async () => await service.DeleteUserAsync(Guid.Empty);

await act.Should().ThrowAsync<ArgumentException>();

await act.Should().ThrowAsync<ArgumentException>()
    .WithMessage("User ID cannot be empty");

// Should NOT throw
Action act = () => service.GetUsers();
act.Should().NotThrow();
```

---

## Afternoon Session (2-3 hours)

### 3.7 Introduction to AutoFixture

#### The Problem

Creating test data is tedious:

```csharp
var user = new User
{
    Id = Guid.NewGuid(),
    FirstName = "Test",
    LastName = "User",
    Email = "test@example.com",
    PhoneNumber = "555-1234",
    Address = "123 Main St",
    City = "Springfield",
    State = "IL",
    ZipCode = "62701",
    Country = "USA",
    CreatedOn = DateTime.UtcNow,
    Active = true,
    RoleId = Guid.NewGuid(),
    // ... and 20 more properties
};
```

**For every single test!**

#### AutoFixture Solution

```csharp
using AutoFixture;

var fixture = new Fixture();

// Generate a complete User object with random data
var user = fixture.Create<User>();

// user.Id = some random Guid
// user.FirstName = some random string
// user.Email = some random string
// ... all properties populated automatically
```

---

### 3.8 AutoFixture Basic Usage

#### Installation

Already in Fujiq.Test:
```xml
<PackageReference Include="AutoFixture" Version="4.18.0" />
<PackageReference Include="AutoFixture.Xunit2" Version="4.18.0" />
```

#### Create Single Object

```csharp
var fixture = new Fixture();

// Create single instance
var user = fixture.Create<User>();
var game = fixture.Create<GamesMaster>();

// All properties are automatically populated with random values
```

#### Create Multiple Objects

```csharp
// Create a collection
var users = fixture.CreateMany<User>().ToList();  // 3 users by default

// Create specific number
var users = fixture.CreateMany<User>(10).ToList();  // 10 users

// Create array
var userArray = fixture.CreateMany<User>(5).ToArray();
```

---

### 3.9 AutoFixture Customization

#### Customize Specific Properties

```csharp
var fixture = new Fixture();

// Customize with .Build()
var user = fixture.Build<User>()
    .With(u => u.Email, "john@example.com")
    .With(u => u.Active, true)
    .Create();

// Result: Email and Active are set, other properties are random
```

#### Without Properties

```csharp
// Don't set the ID (database will assign it)
var user = fixture.Build<User>()
    .Without(u => u.Id)
    .Create();

// Result: Id is default(Guid), other properties are set
```

#### Multiple Customizations

```csharp
var game = fixture.Build<GamesMaster>()
    .With(g => g.Id, Guid.NewGuid())
    .With(g => g.Name, "Fortnite")
    .With(g => g.Active, true)
    .With(g => g.SteamAppId, "578080")
    .Without(g => g.Client)  // Don't create navigation property
    .Create();
```

#### Using Functions for Values

```csharp
var user = fixture.Build<User>()
    .With(u => u.CreatedOn, () => DateTime.UtcNow.AddDays(-30))
    .With(u => u.Email, () => $"user{Guid.NewGuid():N}@example.com")
    .Create();
```

---

### 3.10 Fujiq.Test MockDataBuilders

Fujiq.Test provides custom builders for domain entities.

**File:** `Common/MockDataBuilders.cs`

#### Why Custom Builders?

AutoFixture is great, but:
- Generates completely random data
- Doesn't understand business rules
- Doesn't create realistic values

MockDataBuilders provide:
- Realistic default values
- Business-rule-compliant data
- Helper methods for common scenarios
- Proper relationships between entities

---

### 3.11 ApplicationUserBuilder

```csharp
// Create user with defaults
var user = MockDataBuilders.ApplicationUserBuilder.Create();

// user.FirstName = realistic name
// user.Email = realistic email format
// user.Active = true (default)

// Create user with specific values
var admin = MockDataBuilders.ApplicationUserBuilder.Create(
    firstName: "John",
    lastName: "Doe",
    username: "admin",
    active: true,
    roleId: adminRoleId
);

// Create predefined admin
var admin = MockDataBuilders.ApplicationUserBuilder.CreateAdmin();

// Create inactive user
var inactive = MockDataBuilders.ApplicationUserBuilder.CreateInactiveUser();

// Create list of users
var users = MockDataBuilders.ApplicationUserBuilder.CreateList(10);
```

**What you get:**
- Realistic names ("John Smith", "Jane Doe", etc.)
- Valid email formats
- Proper GUID generation
- Correct date/time values
- Active = true by default

---

### 3.12 GamesMasterBuilder

```csharp
// Create game with defaults
var game = MockDataBuilders.GamesMasterBuilder.Create();

// Create game with specific values
var fortnite = MockDataBuilders.GamesMasterBuilder.Create(
    name: "Fortnite Battle Royale",
    steamAppId: "578080",
    clientId: epicGamesClientId,
    active: true
);

// Create list of games
var games = MockDataBuilders.GamesMasterBuilder.CreateList(5);

// Create game with custom client
var client = MockDataBuilders.ClientBuilder.Create(name: "Epic Games");
var game = MockDataBuilders.GamesMasterBuilder.Create(
    name: "Fortnite",
    clientId: client.Id
);
```

---

### 3.13 Platform-Specific Builders

```csharp
var game = MockDataBuilders.GamesMasterBuilder.Create(name: "Fortnite");

// Steam sales data (7 days by default)
var steamSales = MockDataBuilders.SteamBuilder.CreateList(7, game.Id);

// Xbox sales data
var xboxSales = MockDataBuilders.XboxBuilder.CreateList(7, game.Id);

// PlayStation sales data
var psSales = MockDataBuilders.PlaystationBuilder.CreateList(7, game.Id);

// Nintendo sales data
var nintendoSales = MockDataBuilders.NintendoBuilder.CreateList(7, game.Id);

// All sales data includes:
// - Realistic revenue values
// - Proper date ranges
// - Correct game relationships
// - Valid unit counts
```

---

### 3.14 Complete Test Example with Fujiq Builders

```csharp
[Fact]
public void Complete_Game_Sales_Scenario_Should_Work()
{
    // Arrange - Create a complete gaming ecosystem
    var client = MockDataBuilders.ClientBuilder.Create(name: "Epic Games Studio");

    var game = MockDataBuilders.GamesMasterBuilder.Create(
        name: "Fortnite Battle Royale",
        steamAppId: "578080",
        clientId: client.Id
    );

    // Create 30 days of sales data for all platforms
    var steamSales = MockDataBuilders.SteamBuilder.CreateList(30, game.Id);
    var xboxSales = MockDataBuilders.XboxBuilder.CreateList(30, game.Id);
    var psSales = MockDataBuilders.PlaystationBuilder.CreateList(30, game.Id);
    var nintendoSales = MockDataBuilders.NintendoBuilder.CreateList(30, game.Id);

    // Assert - Verify relationships using FluentAssertions
    client.Name.Should().Be("Epic Games Studio");

    game.ClientId.Should().Be(client.Id);
    game.Name.Should().Be("Fortnite Battle Royale");
    game.SteamAppId.Should().Be("578080");

    steamSales.Should().HaveCount(30);
    steamSales.Should().AllSatisfy(s =>
    {
        s.GamesMasterId.Should().Be(game.Id);
        s.Revenue.Should().BeGreaterThan(0);
    });

    xboxSales.Should().HaveCount(30);
    psSales.Should().HaveCount(30);
    nintendoSales.Should().HaveCount(30);
}
```

---

### 3.15 Hands-On Exercises

#### Exercise 3.1: Convert to FluentAssertions

Rewrite Day 1 tests using FluentAssertions:

```csharp
[Fact]
public void String_Operations_With_FluentAssertions()
{
    // Arrange
    string firstName = "John";
    string lastName = "Doe";

    // Act
    string fullName = $"{firstName} {lastName}";

    // Assert - Using FluentAssertions
    fullName.Should().Be("John Doe");
    fullName.Should().Contain("John");
    fullName.Should().StartWith("John");
    fullName.Should().EndWith("Doe");
    fullName.Should().HaveLength(8);
}

[Fact]
public void Collection_Operations_With_FluentAssertions()
{
    // Arrange
    var users = new List<string> { "John", "Jane", "Bob" };

    // Act
    var activeUsers = users.Where(u => u.StartsWith("J")).ToList();

    // Assert
    activeUsers.Should().HaveCount(2);
    activeUsers.Should().Contain("John");
    activeUsers.Should().Contain("Jane");
    activeUsers.Should().NotContain("Bob");
    activeUsers.Should().AllSatisfy(u => u.Should().StartWith("J"));
}
```

#### Exercise 3.2: Use AutoFixture

```csharp
[Fact]
public void AutoFixture_Should_Generate_Valid_User()
{
    // Arrange
    var fixture = new Fixture();

    // Act
    var user = fixture.Build<User>()
        .With(u => u.Email, "test@example.com")
        .With(u => u.Active, true)
        .Create();

    // Assert
    user.Should().NotBeNull();
    user.Email.Should().Be("test@example.com");
    user.Active.Should().BeTrue();
    user.Id.Should().NotBeEmpty();
    user.FirstName.Should().NotBeNullOrEmpty();
    user.LastName.Should().NotBeNullOrEmpty();
}

[Fact]
public void AutoFixture_Should_Generate_Multiple_Users()
{
    // Arrange & Act
    var fixture = new Fixture();
    var users = fixture.CreateMany<User>(5).ToList();

    // Assert
    users.Should().HaveCount(5);
    users.Should().AllSatisfy(u =>
    {
        u.Id.Should().NotBeEmpty();
        u.FirstName.Should().NotBeNullOrEmpty();
    });
    users.Select(u => u.Id).Should().OnlyHaveUniqueItems();
}
```

#### Exercise 3.3: Use Fujiq MockDataBuilders

Study and use the builders:

```csharp
[Fact]
public void Should_Create_Complete_Game_Ecosystem()
{
    // Arrange & Act
    var client = MockDataBuilders.ClientBuilder.Create(name: "My Game Studio");

    var game = MockDataBuilders.GamesMasterBuilder.Create(
        name: "My Awesome Game",
        clientId: client.Id
    );

    var steamData = MockDataBuilders.SteamBuilder.CreateList(7, game.Id);

    // Assert
    client.Should().NotBeNull();
    client.Name.Should().Be("My Game Studio");

    game.Should().NotBeNull();
    game.ClientId.Should().Be(client.Id);
    game.Name.Should().Be("My Awesome Game");

    steamData.Should().HaveCount(7);
    steamData.Should().AllSatisfy(s =>
    {
        s.GamesMasterId.Should().Be(game.Id);
        s.Revenue.Should().BeGreaterThan(0);
        s.Units.Should().BeGreaterOrEqualTo(0);
    });
}
```

---

### 3.16 Test Data Strategy Guide

**When to use what:**

| Scenario | Tool | Reason |
|----------|------|--------|
| **Simple data types** | Hardcode | Clear and simple |
| **Random data needed** | AutoFixture | Quick generation |
| **Realistic domain entities** | MockDataBuilders | Business-rule compliance |
| **Specific test scenarios** | Hardcode + Builders | Control + convenience |
| **Multiple related entities** | MockDataBuilders | Proper relationships |
| **Integration tests** | MockDataBuilders + DbContext | Realistic data |

**Examples:**

```csharp
// Hardcoded (simple tests)
var name = "John Doe";
var email = "john@example.com";

// AutoFixture (random data)
var user = fixture.Create<User>();

// MockDataBuilders (realistic data)
var user = MockDataBuilders.ApplicationUserBuilder.Create();

// Combination (controlled + realistic)
var user = MockDataBuilders.ApplicationUserBuilder.Create(
    firstName: "John",  // Specific
    lastName: "Doe",    // Specific
    active: true        // Specific
    // Other fields: realistic defaults from builder
);
```

---

## End of Day 3 - Assessment

### Self-Check Questions

1. **Why is `result.Should().Be(expected)` better than `Assert.Equal`?**
   - Answer: More readable, better error messages, natural language syntax

2. **What problem does AutoFixture solve?**
   - Answer: Eliminates tedious test data creation by auto-generating random values

3. **How do you customize AutoFixture-generated objects?**
   - Answer: Use `.Build<T>().With().Create()` pattern

4. **What's the advantage of MockDataBuilders over AutoFixture?**
   - Answer: Realistic, business-rule-compliant data vs. completely random data

5. **When should you use FluentAssertions vs xUnit assertions?**
   - Answer: Always prefer FluentAssertions for readability (except for simple Assert.Null)

### Practical Exercises

Complete these before moving to Day 4:

#### Exercise 1: Rewrite 5 Tests with FluentAssertions
- Take 5 existing tests
- Replace all Assert.* with .Should()
- Add additional assertions using FluentAssertions features

#### Exercise 2: Create 3 Tests Using AutoFixture
- Generate single objects
- Generate collections
- Customize properties with .With()

#### Exercise 3: Create 3 Tests Using MockDataBuilders
- Create related entities (Client + Game)
- Create sales data for multiple platforms
- Verify relationships with FluentAssertions

---

### Homework

#### Reading Assignments

1. **Read:** `Fujiq.Test/Documentation/README_MockData.md`
2. **Study:** `Fujiq.Test/Common/MockDataBuilders.cs`
3. **Review:** `Fujiq.Test/Examples/MockDataUsageExamples.cs`

#### Practice Assignments

1. Rewrite 5 old tests using FluentAssertions
2. Create 3 tests using AutoFixture with customization
3. Create 3 tests using MockDataBuilders for different entities

---

### Key Takeaways

- FluentAssertions makes tests more readable with natural language syntax
- AutoFixture eliminates tedious test data creation
- Use `.Build<T>().With().Create()` to customize AutoFixture objects
- MockDataBuilders provide realistic, business-compliant test data
- Choose the right tool: hardcode simple data, AutoFixture for random, MockDataBuilders for domain entities
- FluentAssertions provides better error messages and is more maintainable

---

### Reference Files

Study these files:
1. `Fujiq.Test/Common/MockDataBuilders.cs` - All builders
2. `Fujiq.Test/Examples/MockDataUsageExamples.cs` - Usage patterns
3. `Fujiq.Test/Services/GamesMasterAppServiceTests.cs` - Real examples

---

**Next:** [Day 4: Test Coverage, Integration Tests & Best Practices](./Day-4-Test-Coverage-Integration-Tests.md)
