# Day 5: Advanced Patterns & Real-World Practice

**Duration:** 4-6 hours
**Prerequisites:** Days 1-4 completed

---

## Learning Objectives

By the end of Day 5, you will:
- Organize tests effectively using regions and fixtures
- Test async code correctly
- Test exception scenarios properly
- Use parametrized tests with complex data
- Avoid common testing mistakes
- Write comprehensive test suites for real services
- Apply all learned concepts to a final project

---

## Morning Session (2-3 hours)

### 5.1 Test Organization

#### File and Folder Structure

```
Fujiq.Test/
├── Services/          # Unit tests for services
│   ├── GamesMasterAppServiceTests.cs
│   ├── UserServiceTests.cs
│   └── EmailServiceTests.cs
├── Controllers/       # Controller tests
│   ├── GameTitleControllerTests.cs
│   └── ReportControllerTests.cs
├── Integration/       # Integration tests
│   └── FujiqDomainIntegrationTests.cs
├── Common/           # Shared test utilities
│   ├── TestBase.cs
│   ├── MockHelpers.cs
│   └── MockDataBuilders.cs
└── Exercises/        # Learning exercises
```

**Best Practices:**
- One test class per class being tested
- Group related tests in folders
- Keep test files near what they test
- Use descriptive folder names

---

#### Using Regions for Grouping

```csharp
public class GamesMasterAppServiceTests
{
    #region Constructor & Setup

    private readonly Mock<IRepository<GamesMaster>> _mockRepository;
    private readonly GamesMasterAppService _service;
    private readonly IFixture _fixture;

    public GamesMasterAppServiceTests()
    {
        _mockRepository = new Mock<IRepository<GamesMaster>>();
        _fixture = new Fixture();
        _service = new GamesMasterAppService(_mockRepository.Object);
    }

    #endregion

    #region GetByIdAsync Tests

    [Fact]
    public async Task GetByIdAsync_ShouldReturnGame_WhenGameExists()
    {
        // Test implementation
    }

    [Fact]
    public async Task GetByIdAsync_ShouldReturnNull_WhenGameDoesNotExist()
    {
        // Test implementation
    }

    #endregion

    #region FindAsync Tests

    [Fact]
    public async Task FindAsync_ShouldReturnMatchingGames()
    {
        // Test implementation
    }

    [Fact]
    public async Task FindAsync_ShouldReturnEmptyList_WhenNoMatches()
    {
        // Test implementation
    }

    #endregion

    #region Add Tests

    [Fact]
    public async Task Add_ShouldAddGame_AndCallSaveChanges()
    {
        // Test implementation
    }

    #endregion

    #region Delete Tests

    [Fact]
    public async Task DeleteAsync_ShouldRemoveGame_WhenExists()
    {
        // Test implementation
    }

    #endregion
}
```

**Benefits:**
- Easy navigation with Visual Studio region collapse
- Clear logical grouping
- Easier to find related tests
- Better code organization

---

#### Class Fixtures for Shared Setup

**When to Use:**
- Expensive setup (database, connections)
- Shared across all tests in a class
- Created once per test class

```csharp
public class DatabaseFixture : IDisposable
{
    public FujiqDbContext Context { get; private set; }

    public DatabaseFixture()
    {
        // Setup once for all tests
        var options = new DbContextOptionsBuilder<FujiqDbContext>()
            .UseInMemoryDatabase("TestDb")
            .Options;

        Context = new FujiqDbContext(options);

        // Seed data
        SeedTestData();
    }

    private void SeedTestData()
    {
        // Add common test data
        Context.Users.Add(new User { Name = "Admin" });
        Context.SaveChanges();
    }

    public void Dispose()
    {
        Context.Dispose();
    }
}

// Use the fixture
public class MyTests : IClassFixture<DatabaseFixture>
{
    private readonly DatabaseFixture _fixture;

    public MyTests(DatabaseFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public void Test1()
    {
        // Use _fixture.Context
    }
}
```

---

### 5.2 Testing Async Code

#### The Correct Pattern

```csharp
[Fact]
public async Task GetUserAsync_ShouldReturnUser()
{
    // Arrange
    var mockRepo = new Mock<IRepository<User>>();
    mockRepo.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
        .ReturnsAsync(new User { Name = "John" });

    var service = new UserService(mockRepo.Object);

    // Act
    var result = await service.GetUserAsync(Guid.NewGuid());

    // Assert
    result.Should().NotBeNull();
    result.Name.Should().Be("John");
}
```

**Key Points:**
- Test method must be `async Task` (not `async void`)
- Use `await` when calling async methods
- Use `.ReturnsAsync()` for mock setup
- Use `await act.Should().ThrowAsync<>()` for exceptions

---

#### Common Pitfalls

**Pitfall 1: Forgetting await**
```csharp
// ❌ BAD - Doesn't actually wait for result
[Fact]
public void Bad_Test()
{
    var result = service.GetUserAsync(id);  // Returns Task<User>, not User!
    Assert.NotNull(result);  // Always passes, testing the Task not the result
}

// ✅ GOOD
[Fact]
public async Task Good_Test()
{
    var result = await service.GetUserAsync(id);
    result.Should().NotBeNull();
}
```

**Pitfall 2: Using async void**
```csharp
// ❌ BAD - async void
[Fact]
public async void Bad_Test()  // void instead of Task
{
    var result = await service.GetUserAsync(id);
}

// ✅ GOOD - async Task
[Fact]
public async Task Good_Test()
{
    var result = await service.GetUserAsync(id);
}
```

**Pitfall 3: Not using ReturnsAsync**
```csharp
// ❌ BAD - Using Returns for async method
mockRepo.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
    .Returns(new User());  // Wrong!

// ✅ GOOD - Using ReturnsAsync
mockRepo.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
    .ReturnsAsync(new User());
```

---

### 5.3 Testing Exceptions

#### Pattern 1: FluentAssertions (Recommended)

**Synchronous:**
```csharp
[Fact]
public void Delete_Should_Throw_When_UserNotFound()
{
    // Arrange
    var mockRepo = new Mock<IRepository<User>>();
    mockRepo.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
        .ReturnsAsync((User)null);

    var service = new UserService(mockRepo.Object);

    // Act
    Action act = () => service.DeleteUser(Guid.NewGuid());

    // Assert
    act.Should().Throw<NotFoundException>()
        .WithMessage("User not found");
}
```

**Asynchronous:**
```csharp
[Fact]
public async Task DeleteAsync_Should_Throw_When_UserNotFound()
{
    // Arrange
    var mockRepo = new Mock<IRepository<User>>();
    mockRepo.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
        .ReturnsAsync((User)null);

    var service = new UserService(mockRepo.Object);

    // Act
    Func<Task> act = async () => await service.DeleteUserAsync(Guid.NewGuid());

    // Assert
    await act.Should().ThrowAsync<NotFoundException>()
        .WithMessage("User not found");
}
```

**With Specific Properties:**
```csharp
await act.Should().ThrowAsync<ValidationException>()
    .WithMessage("*email*")  // Wildcards
    .Where(ex => ex.ErrorCode == "INVALID_EMAIL");
```

#### Pattern 2: xUnit Assert

**Synchronous:**
```csharp
[Fact]
public void Delete_Should_Throw_When_UserNotFound()
{
    // Arrange
    var service = new UserService(mockRepo.Object);

    // Act & Assert
    var exception = Assert.Throws<NotFoundException>(
        () => service.DeleteUser(Guid.NewGuid())
    );

    Assert.Equal("User not found", exception.Message);
}
```

**Asynchronous:**
```csharp
[Fact]
public async Task DeleteAsync_Should_Throw_When_UserNotFound()
{
    // Act & Assert
    var exception = await Assert.ThrowsAsync<NotFoundException>(
        () => service.DeleteUserAsync(Guid.NewGuid())
    );

    Assert.Equal("User not found", exception.Message);
}
```

---

### 5.4 Parametrized Tests with Complex Data

#### MemberData Pattern

```csharp
public class CurrencyTests
{
    public static IEnumerable<object[]> GetCurrencyTestData()
    {
        yield return new object[] { "USD", 1.0, 100m, 100m };
        yield return new object[] { "EUR", 0.85, 100m, 85m };
        yield return new object[] { "GBP", 0.73, 100m, 73m };
        yield return new object[] { "JPY", 110.0, 100m, 11000m };
    }

    [Theory]
    [MemberData(nameof(GetCurrencyTestData))]
    public void Should_Convert_Currency(
        string currency,
        decimal rate,
        decimal amount,
        decimal expected)
    {
        // Arrange
        var converter = new CurrencyConverter();

        // Act
        var result = converter.Convert(currency, "USD", amount, rate);

        // Assert
        result.Should().BeApproximately(expected, 0.01m);
    }
}
```

#### ClassData Pattern

```csharp
public class CurrencyTestData : IEnumerable<object[]>
{
    public IEnumerator<object[]> GetEnumerator()
    {
        yield return new object[] { "USD", 1.0m, 100m, 100m };
        yield return new object[] { "EUR", 0.85m, 100m, 85m };
        yield return new object[] { "GBP", 0.73m, 100m, 73m };
    }

    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();
}

[Theory]
[ClassData(typeof(CurrencyTestData))]
public void Should_Convert_Currency(
    string currency,
    decimal rate,
    decimal amount,
    decimal expected)
{
    var converter = new CurrencyConverter();
    var result = converter.Convert(currency, "USD", amount, rate);
    result.Should().BeApproximately(expected, 0.01m);
}
```

#### Complex Object Data

```csharp
public static IEnumerable<object[]> GetUserTestData()
{
    yield return new object[]
    {
        new User { Name = "Admin", Role = "Admin" },
        true  // Expected: IsAuthorized
    };

    yield return new object[]
    {
        new User { Name = "User", Role = "User" },
        false  // Expected: IsAuthorized
    };
}

[Theory]
[MemberData(nameof(GetUserTestData))]
public void Should_Check_Authorization(User user, bool expectedAuth)
{
    var result = _authService.IsAuthorized(user);
    result.Should().Be(expectedAuth);
}
```

---

## Afternoon Session (2-3 hours)

### 5.5 Real-World Test Case Study

**Study File:** `Services/GamesMasterAppServiceTests.cs`

This file demonstrates:
- Complete service testing (965 lines, 64 tests)
- All CRUD operations
- Mock setup and verification
- FluentAssertions usage
- Test organization with regions
- Both success and failure scenarios

**Key Methods Tested:**
```csharp
#region GetByIdAsync Tests
// - Success scenario
// - Not found scenario

#region FindAsync Tests
// - With filters
// - Empty results

#region Add Tests
// - Valid game
// - Null validation

#region Update Tests
// - Exists
// - Not found

#region Delete Tests
// - By ID
// - Cascade delete
```

**Learning Points:**
1. Constructor creates all mocks once
2. Each test sets up specific mock behavior
3. Verifies both return values AND mock calls
4. Tests edge cases (null, empty, exceptions)
5. Uses descriptive test names

---

### 5.6 Common Testing Mistakes to Avoid

#### Mistake 1: Testing Implementation Instead of Behavior

**❌ Bad - Testing Internal Details:**
```csharp
[Fact]
public void Bad_Test()
{
    var user = new User();
    user.FirstName = "John";

    Assert.Equal("John", user.FirstName);  // Just testing property assignment
}
```

**✅ Good - Testing Behavior:**
```csharp
[Fact]
public void GetFullName_Should_Combine_FirstAndLastName()
{
    // Arrange
    var user = new User
    {
        FirstName = "John",
        LastName = "Doe"
    };

    // Act
    var fullName = user.GetFullName();

    // Assert
    fullName.Should().Be("John Doe");
}
```

---

#### Mistake 2: Testing Multiple Things in One Test

**❌ Bad - Too Many Assertions:**
```csharp
[Fact]
public async Task Bad_Test()
{
    // Create
    var user = await service.CreateUser("john");
    user.Should().NotBeNull();
    user.Name.Should().Be("john");

    // Update
    user.Name = "jane";
    await service.Update(user);

    // Delete
    var deleted = await service.Delete(user.Id);
    deleted.Should().BeTrue();

    // Verify deleted
    var found = await service.Find(user.Id);
    found.Should().BeNull();
}
```

**✅ Good - Separate Tests:**
```csharp
[Fact]
public async Task CreateUser_Should_ReturnUser()
{
    var user = await service.CreateUser("john");
    user.Should().NotBeNull();
    user.Name.Should().Be("john");
}

[Fact]
public async Task UpdateUser_Should_ModifyUser()
{
    // Test only update logic
}

[Fact]
public async Task DeleteUser_Should_RemoveUser()
{
    // Test only delete logic
}
```

---

#### Mistake 3: Not Using AAA Pattern

**❌ Bad - Mixed Sections:**
```csharp
[Fact]
public void Bad_Test()
{
    var service = new UserService(mockRepo.Object);
    var user = service.GetUser(id);
    user.Should().NotBeNull();
    var name = user.Name;
    name.Should().Be("John");
    var email = user.Email;
    email.Should().Contain("@");
}
```

**✅ Good - Clear AAA:**
```csharp
[Fact]
public void Good_Test()
{
    // Arrange
    var service = new UserService(mockRepo.Object);

    // Act
    var user = service.GetUser(id);

    // Assert
    user.Should().NotBeNull();
    user.Name.Should().Be("John");
    user.Email.Should().Contain("@");
}
```

---

#### Mistake 4: Brittle Tests (Over-Specified)

**❌ Bad - Too Specific:**
```csharp
[Fact]
public void Bad_Test()
{
    var users = service.GetActiveUsers();

    // Will break if order changes or count changes
    users.Should().HaveCount(5);
    users[0].Name.Should().Be("John");
    users[1].Name.Should().Be("Jane");
    users[2].Name.Should().Be("Bob");
    // ...
}
```

**✅ Good - Test What Matters:**
```csharp
[Fact]
public void GetActiveUsers_Should_ReturnOnlyActiveUsers()
{
    var users = service.GetActiveUsers();

    users.Should().NotBeEmpty();
    users.Should().AllSatisfy(u => u.Active.Should().BeTrue());
}
```

---

#### Mistake 5: Not Testing Edge Cases

**❌ Only Happy Path:**
```csharp
[Fact]
public void Divide_Should_ReturnCorrectResult()
{
    var result = calculator.Divide(10, 2);
    result.Should().Be(5);
}
```

**✅ Test Edge Cases Too:**
```csharp
[Theory]
[InlineData(10, 2, 5)]       // Normal case
[InlineData(0, 5, 0)]        // Zero dividend
[InlineData(-10, 2, -5)]     // Negative numbers
[InlineData(10, -2, -5)]     // Negative divisor
public void Divide_Should_HandleAllCases(int a, int b, int expected)
{
    var result = calculator.Divide(a, b);
    result.Should().Be(expected);
}

[Fact]
public void Divide_Should_ThrowException_WhenDivideByZero()
{
    Action act = () => calculator.Divide(10, 0);
    act.Should().Throw<DivideByZeroException>();
}
```

---

### 5.7 Final Hands-On Project

**Project: Test a Complete Service**

Create a comprehensive test suite for `GameService` or any service in Fujiq.

#### Requirements

1. **Test all public methods**
2. **Test success scenarios**
3. **Test failure scenarios**
4. **Test edge cases**
5. **Use AAA pattern consistently**
6. **Use FluentAssertions**
7. **Use MockDataBuilders for test data**
8. **Aim for >80% coverage**
9. **Follow Fujiq naming conventions**
10. **Organize with regions**

#### Example Service Interface

```csharp
public interface IGameService
{
    Task<Game> GetByIdAsync(Guid id);
    Task<List<Game>> GetActiveGamesAsync();
    Task<List<Game>> SearchGamesAsync(string searchTerm);
    Task<Game> CreateAsync(Game game);
    Task<bool> UpdateAsync(Game game);
    Task DeleteAsync(Guid id);
}
```

#### Expected Tests (Minimum 20)

**GetByIdAsync:**
- `GetByIdAsync_ShouldReturnGame_WhenGameExists`
- `GetByIdAsync_ShouldReturnNull_WhenGameDoesNotExist`
- `GetByIdAsync_ShouldThrowException_WhenIdIsEmpty`

**GetActiveGamesAsync:**
- `GetActiveGamesAsync_ShouldReturnOnlyActiveGames`
- `GetActiveGamesAsync_ShouldReturnEmptyList_WhenNoActiveGames`

**SearchGamesAsync:**
- `SearchGamesAsync_ShouldReturnMatchingGames_WhenSearchTermMatches`
- `SearchGamesAsync_ShouldReturnEmptyList_WhenNoMatches`
- `SearchGamesAsync_ShouldThrowException_WhenSearchTermIsNull`

**CreateAsync:**
- `CreateAsync_ShouldSaveGame_AndReturnWithId`
- `CreateAsync_ShouldThrowException_WhenGameIsNull`
- `CreateAsync_ShouldThrowException_WhenGameNameIsEmpty`
- `CreateAsync_ShouldCallRepository_AndSaveChanges`

**UpdateAsync:**
- `UpdateAsync_ShouldReturnTrue_WhenGameExists`
- `UpdateAsync_ShouldReturnFalse_WhenGameDoesNotExist`
- `UpdateAsync_ShouldThrowException_WhenGameIsNull`
- `UpdateAsync_ShouldCallRepository_AndSaveChanges`

**DeleteAsync:**
- `DeleteAsync_ShouldRemoveGame_WhenGameExists`
- `DeleteAsync_ShouldThrowException_WhenGameDoesNotExist`
- `DeleteAsync_ShouldThrowException_WhenIdIsEmpty`
- `DeleteAsync_ShouldCallRepository_AndSaveChanges`

#### Template

```csharp
using Moq;
using Xunit;
using FluentAssertions;
using AutoFixture;

namespace Fujiq.Test.Services
{
    public class GameServiceTests
    {
        #region Constructor & Setup

        private readonly Mock<IRepository<Game>> _mockRepository;
        private readonly Mock<ILogger<GameService>> _mockLogger;
        private readonly GameService _service;
        private readonly IFixture _fixture;

        public GameServiceTests()
        {
            _mockRepository = new Mock<IRepository<Game>>();
            _mockLogger = MockHelpers.CreateLogger<GameService>();
            _fixture = new Fixture();

            _service = new GameService(
                _mockRepository.Object,
                _mockLogger.Object
            );
        }

        #endregion

        #region GetByIdAsync Tests

        [Fact]
        public async Task GetByIdAsync_ShouldReturnGame_WhenGameExists()
        {
            // Arrange
            var gameId = Guid.NewGuid();
            var expectedGame = MockDataBuilders.GamesMasterBuilder.Create(
                name: "Test Game"
            );
            expectedGame.Id = gameId;

            _mockRepository.Setup(r => r.GetByIdAsync(gameId))
                .ReturnsAsync(expectedGame);

            // Act
            var result = await _service.GetByIdAsync(gameId);

            // Assert
            result.Should().NotBeNull();
            result.Id.Should().Be(gameId);
            result.Name.Should().Be("Test Game");

            _mockRepository.Verify(
                r => r.GetByIdAsync(gameId),
                Times.Once
            );
        }

        // Add more tests...

        #endregion

        // Add more regions...
    }
}
```

---

## End of Day 5 - Final Assessment

### Comprehensive Quiz

1. What are the three A's in the AAA pattern?
2. When should you use [Fact] vs [Theory]?
3. What's the purpose of mocking?
4. How do you verify a mock was called?
5. What's the difference between .Returns() and .ReturnsAsync()?
6. Why use FluentAssertions over xUnit assertions?
7. What's the benefit of AutoFixture?
8. What are the different types of code coverage?
9. When should you write integration tests vs unit tests?
10. Name 3 common testing mistakes and how to avoid them

### Final Project Checklist

- [ ] Created test class for complete service
- [ ] Written 20+ tests
- [ ] All tests follow AAA pattern
- [ ] Used FluentAssertions for all assertions
- [ ] Used MockDataBuilders for test data
- [ ] Tested all public methods
- [ ] Tested success scenarios
- [ ] Tested failure scenarios
- [ ] Tested edge cases
- [ ] Organized with regions
- [ ] Used descriptive test names
- [ ] All mocks verified
- [ ] Achieved >80% code coverage
- [ ] All tests pass
- [ ] Code follows Fujiq.Test conventions

---

## Course Completion Summary

### What You've Learned

**Day 1:**
- Unit testing fundamentals
- Testing pyramid
- xUnit framework
- AAA pattern
- Test naming conventions

**Day 2:**
- Mocking with Moq
- Mock setup and verification
- Test isolation
- It.IsAny and It.Is patterns

**Day 3:**
- FluentAssertions
- AutoFixture
- MockDataBuilders
- Test data strategies

**Day 4:**
- Code coverage types and goals
- What to test (and what not to)
- Integration tests
- TestBase pattern
- Writing testable code

**Day 5:**
- Test organization
- Testing async code
- Testing exceptions
- Advanced parametrization
- Common mistakes
- Complete test suites

---

### Certification Criteria

To be considered proficient in unit testing for Fujiq.Test:

- [ ] Completed all 5 days of training
- [ ] Completed all exercises and homework
- [ ] Written final project (20+ tests)
- [ ] Achieved >80% coverage on final project
- [ ] Passed final assessment quiz (8/10 correct)
- [ ] Demonstrated understanding of all core concepts
- [ ] Can write tests independently

---

### Next Steps

#### Continue Practicing
1. Write tests for new features before coding (TDD)
2. Add tests to existing code
3. Review and improve old tests
4. Participate in code reviews focusing on tests

#### Advanced Topics to Explore
1. **Test-Driven Development (TDD)**
   - Write tests first, then code
   - Red-Green-Refactor cycle

2. **Behavior-Driven Development (BDD)**
   - SpecFlow/Gherkin
   - Given-When-Then scenarios

3. **Property-Based Testing**
   - FsCheck library
   - Generate thousands of test cases

4. **Performance Testing**
   - BenchmarkDotNet
   - Load testing

5. **Mutation Testing**
   - Stryker.NET
   - Test your tests

---

## Congratulations!

You've completed the 5-Day Unit Testing Learning Plan for Fujiq.Test!

You now have:
- ✅ Strong foundation in unit testing principles
- ✅ Practical experience with xUnit framework
- ✅ Ability to write effective mocks with Moq
- ✅ Skills to write readable tests with FluentAssertions
- ✅ Knowledge of test data generation
- ✅ Understanding of code coverage
- ✅ Experience with integration tests
- ✅ Familiarity with Fujiq.Test patterns

**Keep testing, keep learning, and keep improving!**

---

## Additional Resources

### Fujiq.Test Files to Study
1. `Services/GamesMasterAppServiceTests.cs` - Comprehensive example
2. `Common/MockDataBuilders.cs` - Data builder patterns
3. `Integration/FujiqDomainIntegrationTests.cs` - Integration examples
4. `Common/TestBase.cs` - Base test infrastructure
5. `Examples/MockDataUsageExamples.cs` - Usage examples

### External Resources
1. [xUnit Documentation](https://xunit.net/)
2. [Moq Documentation](https://github.com/moq/moq4)
3. [FluentAssertions Documentation](https://fluentassertions.com/)
4. [AutoFixture Documentation](https://github.com/AutoFixture/AutoFixture)
5. [The Art of Unit Testing](https://www.artofunittesting.com/) by Roy Osherove

### Community
- Stack Overflow - tag: `xunit`, `moq`, `unit-testing`
- Reddit - r/dotnet
- .NET Foundation - Discord
- Fujiq.Test team - Internal knowledge sharing

---

**Completed:** Day 5 of 5 ✅

**Previous:** [Day 4: Test Coverage, Integration Tests & Best Practices](./Day-4-Test-Coverage-Integration-Tests.md)

**Back to:** [Learning Plan Overview](./Learning-Plan-Overview.md)
