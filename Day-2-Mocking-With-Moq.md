# Day 2: Mocking with Moq & Test Isolation

**Duration:** 4-6 hours
**Prerequisites:** Day 1 completed, understanding of interfaces and dependency injection

---

## Learning Objectives

By the end of Day 2, you will:
- Understand why mocking is essential for unit testing
- Create mock objects using Moq framework
- Set up mock behaviors with different return values
- Verify that mocks were called correctly
- Test classes with multiple dependencies
- Apply mocking patterns used in Fujiq.Test

---

## Morning Session (2-3 hours)

### 2.1 Why Mocking?

#### The Problem

Consider this real-world service:

```csharp
public class GameService
{
    private readonly IRepository<Game> _repository;
    private readonly IEmailService _emailService;
    private readonly ILogger<GameService> _logger;

    public GameService(
        IRepository<Game> repository,
        IEmailService emailService,
        ILogger<GameService> logger)
    {
        _repository = repository;
        _emailService = emailService;
        _logger = logger;
    }

    public async Task<Game> GetGameAsync(Guid id)
    {
        _logger.LogInformation($"Getting game {id}");

        var game = await _repository.GetByIdAsync(id);

        if (game == null)
        {
            await _emailService.SendAlert($"Game {id} not found");
        }

        return game;
    }
}
```

#### The Challenge

How do you test this WITHOUT:
- A real database (slow, needs setup, hard to control)
- Sending real emails (side effects, external dependencies)
- Complex logging infrastructure

**Answer: MOCKING!**

#### What is a Mock?

A mock is a **fake implementation** of an interface that:
- Simulates the behavior of real objects
- Can be configured to return specific values
- Tracks how it was called
- Requires no real infrastructure

#### Benefits of Mocking

1. **Isolation:** Test only the code you're testing
2. **Speed:** No database, network, or I/O operations
3. **Control:** Control exactly what dependencies return
4. **Reliability:** No external failures
5. **Simplicity:** No complex setup required

---

### 2.2 Introduction to Moq

Moq is a mocking framework that creates fake implementations of interfaces.

#### Installing Moq

Already included in Fujiq.Test:
```xml
<PackageReference Include="Moq" Version="4.20.69" />
```

#### Basic Mock Creation

```csharp
using Moq;

// Create a mock of an interface
var mockRepository = new Mock<IRepository<Game>>();

// Setup what the mock should return
mockRepository.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
    .ReturnsAsync(new Game { Id = Guid.NewGuid(), Name = "Test Game" });

// Use the mock object
IRepository<Game> repository = mockRepository.Object;

// Now you can use repository in your tests
var game = await repository.GetByIdAsync(Guid.NewGuid());
// game.Name will be "Test Game"
```

#### Key Concepts

**Mock<T>** - The mock wrapper
- `new Mock<IRepository<Game>>()` creates the mock

**Setup()** - Configure behavior
- Tells the mock what to return when called

**Object** - The actual mock instance
- `mockRepository.Object` gets the `IRepository<Game>` instance

**.ReturnsAsync()** - For async methods
- `.Returns()` for synchronous methods
- `.ReturnsAsync()` for async methods

---

### 2.3 Mock Setup Patterns

#### 1. Simple Return Value

```csharp
var mockRepository = new Mock<IRepository<Game>>();

// Setup: When GetByIdAsync is called with specific ID, return a specific game
var gameId = Guid.NewGuid();
var expectedGame = new Game { Id = gameId, Name = "Fortnite" };

mockRepository.Setup(r => r.GetByIdAsync(gameId))
    .ReturnsAsync(expectedGame);

// Usage
var result = await mockRepository.Object.GetByIdAsync(gameId);
// result will be expectedGame
```

#### 2. Match Any Value with It.IsAny<T>()

```csharp
// Setup: Return the same game for ANY Guid
mockRepository.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
    .ReturnsAsync(new Game { Name = "Any Game" });

// Will work with any Guid
var result1 = await mockRepository.Object.GetByIdAsync(Guid.NewGuid());
var result2 = await mockRepository.Object.GetByIdAsync(Guid.NewGuid());
// Both return the same game
```

#### 3. Return Based on Input Parameter

```csharp
// Setup: Return a game with the ID that was requested
mockRepository.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
    .ReturnsAsync((Guid id) => new Game { Id = id, Name = $"Game {id}" });

// Usage
var id1 = Guid.NewGuid();
var result = await mockRepository.Object.GetByIdAsync(id1);
// result.Id == id1
```

#### 4. Return Null

```csharp
// Setup: Return null (game not found scenario)
mockRepository.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
    .ReturnsAsync((Game)null);

// Usage
var result = await mockRepository.Object.GetByIdAsync(Guid.NewGuid());
// result is null
```

#### 5. Throw Exception

```csharp
// Setup: Throw exception when called
mockRepository.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
    .ThrowsAsync(new NotFoundException("Game not found"));

// Usage
await Assert.ThrowsAsync<NotFoundException>(
    () => mockRepository.Object.GetByIdAsync(Guid.NewGuid())
);
```

#### 6. Different Returns for Different Inputs

```csharp
var validId = Guid.NewGuid();
var invalidId = Guid.NewGuid();

// Setup: Different behavior based on input
mockRepository.Setup(r => r.GetByIdAsync(validId))
    .ReturnsAsync(new Game { Id = validId });

mockRepository.Setup(r => r.GetByIdAsync(invalidId))
    .ReturnsAsync((Game)null);

// Usage
var found = await mockRepository.Object.GetByIdAsync(validId);      // Returns game
var notFound = await mockRepository.Object.GetByIdAsync(invalidId); // Returns null
```

---

### 2.4 Mock Verification

Verify that your code called the dependencies correctly.

#### Why Verify?

You want to ensure your code:
- Called the repository
- Called it with the right parameters
- Called it the correct number of times
- Didn't call methods it shouldn't have

#### Basic Verification

```csharp
[Fact]
public async Task GetGame_Should_Call_Repository()
{
    // Arrange
    var mockRepository = new Mock<IRepository<Game>>();
    var gameId = Guid.NewGuid();

    mockRepository.Setup(r => r.GetByIdAsync(gameId))
        .ReturnsAsync(new Game());

    var service = new GameService(mockRepository.Object);

    // Act
    await service.GetGameAsync(gameId);

    // Assert - Verify the repository was called
    mockRepository.Verify(
        r => r.GetByIdAsync(gameId),
        Times.Once
    );
}
```

#### Verification Options

```csharp
// Verify called exactly once
mockRepository.Verify(r => r.GetByIdAsync(gameId), Times.Once);

// Verify called exactly N times
mockRepository.Verify(r => r.GetByIdAsync(gameId), Times.Exactly(3));

// Verify never called
mockEmailService.Verify(
    e => e.SendAlert(It.IsAny<string>()),
    Times.Never
);

// Verify called at least once
mockLogger.Verify(
    l => l.LogInformation(It.IsAny<string>()),
    Times.AtLeastOnce
);

// Verify called at most N times
mockRepository.Verify(r => r.SaveChangesAsync(), Times.AtMost(1));
```

#### Verify with Parameters

```csharp
// Verify called with specific value
mockRepository.Verify(
    r => r.GetByIdAsync(specificGuid),
    Times.Once
);

// Verify called with any value
mockRepository.Verify(
    r => r.GetByIdAsync(It.IsAny<Guid>()),
    Times.Once
);

// Verify called with value matching condition
mockRepository.Verify(
    r => r.Add(It.Is<Game>(g => g.Name == "Fortnite")),
    Times.Once
);
```

---

## Afternoon Session (2-3 hours)

### 2.5 Real-World Example from Fujiq.Test

Study this example from `Services/GamesMasterAppServiceTests.cs`:

```csharp
public class GamesMasterAppServiceTests
{
    private readonly Mock<IRepository<GamesMaster>> _mockRepository;
    private readonly Mock<ILogger<GamesMasterAppService>> _mockLogger;
    private readonly GamesMasterAppService _service;
    private readonly IFixture _fixture;

    public GamesMasterAppServiceTests()
    {
        // Arrange - Create mocks
        _mockRepository = new Mock<IRepository<GamesMaster>>();
        _mockLogger = new Mock<ILogger<GamesMasterAppService>>();
        _fixture = new Fixture();

        // Create service with mocked dependencies
        _service = new GamesMasterAppService(
            _mockRepository.Object,
            _mockLogger.Object
        );
    }

    [Fact]
    public async Task GetByIdAsync_ShouldReturnGame_WhenGameExists()
    {
        // Arrange
        var gameId = Guid.NewGuid();
        var expectedGame = new GamesMaster
        {
            Id = gameId,
            Name = "Test Game",
            Active = true
        };

        _mockRepository.Setup(r => r.GetByIdAsync(gameId))
            .ReturnsAsync(expectedGame);

        // Act
        var result = await _service.GetByIdAsync(gameId);

        // Assert
        Assert.NotNull(result);
        Assert.Equal(gameId, result.Id);
        Assert.Equal("Test Game", result.Name);

        // Verify the repository was called correctly
        _mockRepository.Verify(
            r => r.GetByIdAsync(gameId),
            Times.Once
        );
    }

    [Fact]
    public async Task GetByIdAsync_ShouldReturnNull_WhenGameDoesNotExist()
    {
        // Arrange
        var gameId = Guid.NewGuid();

        _mockRepository.Setup(r => r.GetByIdAsync(gameId))
            .ReturnsAsync((GamesMaster)null);

        // Act
        var result = await _service.GetByIdAsync(gameId);

        // Assert
        Assert.Null(result);
        _mockRepository.Verify(
            r => r.GetByIdAsync(gameId),
            Times.Once
        );
    }

    [Fact]
    public async Task Add_ShouldCallRepository_AndSaveChanges()
    {
        // Arrange
        var newGame = new GamesMaster
        {
            Name = "New Game",
            Active = true
        };

        _mockRepository.Setup(r => r.Add(It.IsAny<GamesMaster>()))
            .Returns((GamesMaster g) => g);

        _mockRepository.Setup(r => r.SaveChangesAsync())
            .ReturnsAsync(1);

        // Act
        await _service.AddAsync(newGame);

        // Assert
        _mockRepository.Verify(
            r => r.Add(It.Is<GamesMaster>(g => g.Name == "New Game")),
            Times.Once
        );
        _mockRepository.Verify(
            r => r.SaveChangesAsync(),
            Times.Once
        );
    }
}
```

#### Key Patterns in This Example

1. **Constructor Setup:** Mocks created once in constructor
2. **_fixture Usage:** AutoFixture for generating test data (Day 3)
3. **Mock.Object:** Pass `.Object` to the service constructor
4. **Both Scenarios:** Test success (game found) and failure (null)
5. **Verification:** Always verify mocks were called correctly

---

### 2.6 Hands-On Exercises

#### Exercise 2.1: Basic Mocking

Create: `Exercises/Day2_MockingTests.cs`

```csharp
using Moq;
using Xunit;

namespace Fujiq.Test.Exercises
{
    public class Day2_MockingTests
    {
        #region Basic Repository Mocking

        [Fact]
        public async Task UserService_GetUser_Should_Call_Repository()
        {
            // Arrange
            var mockRepo = new Mock<IUserRepository>();
            var userId = Guid.NewGuid();
            var expectedUser = new User
            {
                Id = userId,
                Name = "John Doe",
                Email = "john@example.com"
            };

            mockRepo.Setup(r => r.GetByIdAsync(userId))
                .ReturnsAsync(expectedUser);

            var userService = new UserService(mockRepo.Object);

            // Act
            var result = await userService.GetUserAsync(userId);

            // Assert
            Assert.NotNull(result);
            Assert.Equal(userId, result.Id);
            Assert.Equal("John Doe", result.Name);

            // Verify
            mockRepo.Verify(r => r.GetByIdAsync(userId), Times.Once);
        }

        [Fact]
        public async Task UserService_GetUser_Should_ReturnNull_When_NotFound()
        {
            // Arrange
            var mockRepo = new Mock<IUserRepository>();
            var userId = Guid.NewGuid();

            mockRepo.Setup(r => r.GetByIdAsync(userId))
                .ReturnsAsync((User)null);

            var userService = new UserService(mockRepo.Object);

            // Act
            var result = await userService.GetUserAsync(userId);

            // Assert
            Assert.Null(result);
            mockRepo.Verify(r => r.GetByIdAsync(userId), Times.Once);
        }

        #endregion

        #region Exception Testing

        [Fact]
        public async Task UserService_Should_Throw_When_Repository_Fails()
        {
            // Arrange
            var mockRepo = new Mock<IUserRepository>();

            mockRepo.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
                .ThrowsAsync(new DatabaseException("Connection failed"));

            var userService = new UserService(mockRepo.Object);

            // Act & Assert
            await Assert.ThrowsAsync<DatabaseException>(
                () => userService.GetUserAsync(Guid.NewGuid())
            );
        }

        #endregion
    }

    #region Test Classes and Interfaces

    public interface IUserRepository
    {
        Task<User> GetByIdAsync(Guid id);
        Task<bool> AddAsync(User user);
    }

    public class User
    {
        public Guid Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }

    public class UserService
    {
        private readonly IUserRepository _repository;

        public UserService(IUserRepository repository)
        {
            _repository = repository;
        }

        public async Task<User> GetUserAsync(Guid id)
        {
            return await _repository.GetByIdAsync(id);
        }
    }

    public class DatabaseException : Exception
    {
        public DatabaseException(string message) : base(message) { }
    }

    #endregion
}
```

#### Exercise 2.2: Mock Multiple Dependencies

Test a service with multiple dependencies:

```csharp
[Fact]
public async Task EmailService_SendWelcomeEmail_Should_UseCorrectTemplate()
{
    // Arrange
    var mockTemplateService = new Mock<ITemplateService>();
    var mockSmtpService = new Mock<ISmtpService>();
    var mockLogger = new Mock<ILogger<EmailService>>();

    mockTemplateService.Setup(t => t.GetTemplate("Welcome"))
        .Returns("<html>Welcome {name}</html>");

    mockSmtpService.Setup(s => s.SendAsync(
            It.IsAny<string>(),
            It.IsAny<string>(),
            It.IsAny<string>()))
        .ReturnsAsync(true);

    var emailService = new EmailService(
        mockTemplateService.Object,
        mockSmtpService.Object,
        mockLogger.Object
    );

    // Act
    await emailService.SendWelcomeEmail("john@example.com", "John");

    // Assert
    mockTemplateService.Verify(
        t => t.GetTemplate("Welcome"),
        Times.Once
    );

    mockSmtpService.Verify(
        s => s.SendAsync(
            "john@example.com",
            It.IsAny<string>(),
            It.Is<string>(body => body.Contains("John"))),
        Times.Once
    );

    // Verify logger was used
    mockLogger.Verify(
        l => l.LogInformation(It.IsAny<string>()),
        Times.AtLeastOnce
    );
}
```

#### Exercise 2.3: Test with It.Is<T>() Conditions

```csharp
[Fact]
public async Task GameService_AddGame_Should_ValidateGame()
{
    // Arrange
    var mockRepo = new Mock<IRepository<Game>>();

    mockRepo.Setup(r => r.Add(It.Is<Game>(g =>
            g.Name != null &&
            g.Name.Length > 0 &&
            g.Active == true
        )))
        .Returns((Game g) => g);

    var service = new GameService(mockRepo.Object);

    // Act
    var newGame = new Game
    {
        Name = "Fortnite",
        Active = true
    };

    await service.AddGameAsync(newGame);

    // Assert
    mockRepo.Verify(
        r => r.Add(It.Is<Game>(g =>
            g.Name == "Fortnite" &&
            g.Active == true
        )),
        Times.Once
    );
}
```

---

### 2.7 Common Moq Patterns in Fujiq.Test

#### 1. It.IsAny<T>() - Match Any Value

```csharp
// Setup accepts any Guid
mockRepository.Setup(r => r.GetByIdAsync(It.IsAny<Guid>()))
    .ReturnsAsync(new Game());

// Verify with any value
mockRepository.Verify(
    r => r.Add(It.IsAny<Game>()),
    Times.Once
);
```

**When to use:**
- You don't care about the specific value
- Testing general behavior
- Value is generated/random

#### 2. It.Is<T>() - Match Specific Condition

```csharp
// Setup with condition
mockRepository.Setup(r => r.Find(
    It.Is<Expression<Func<Game, bool>>>(expr => true)
)).ReturnsAsync(new List<Game>());

// Verify with condition
mockRepository.Verify(
    r => r.Add(It.Is<Game>(g => g.Active == true)),
    Times.Once
);
```

**When to use:**
- Need to verify specific property values
- Testing validation logic
- Complex matching logic

#### 3. Callback - Execute Code When Mock is Called

```csharp
bool wasCalled = false;
Game capturedGame = null;

mockRepository.Setup(r => r.Add(It.IsAny<Game>()))
    .Callback<Game>(game =>
    {
        wasCalled = true;
        capturedGame = game;
    })
    .Returns((Game g) => g);

// Act
service.AddGame(new Game { Name = "Test" });

// Assert
Assert.True(wasCalled);
Assert.Equal("Test", capturedGame.Name);
```

**When to use:**
- Need to capture arguments
- Want to execute custom logic
- Testing side effects

#### 4. MockBehavior.Strict

```csharp
// Strict mock: throws exception if called without setup
var strictMock = new Mock<IRepository<Game>>(MockBehavior.Strict);

// This will throw if GetByIdAsync is called without setup
```

**When to use:**
- Want to ensure only expected methods are called
- Testing that code doesn't call unwanted methods
- More strict testing

---

### 2.8 Mocking Helpers in Fujiq.Test

Study: `Common/MockHelpers.cs`

#### CreateLogger<T>()

```csharp
public static Mock<ILogger<T>> CreateLogger<T>()
{
    var logger = new Mock<ILogger<T>>();
    logger.Setup(x => x.Log(
        It.IsAny<LogLevel>(),
        It.IsAny<EventId>(),
        It.IsAny<object>(),
        It.IsAny<Exception>(),
        It.IsAny<Func<object, Exception, string>>()
    )).Verifiable();

    return logger;
}

// Usage
var mockLogger = MockHelpers.CreateLogger<GameService>();
var service = new GameService(mockLogger.Object);
```

#### CreateMockDbSet<T>()

```csharp
public static Mock<DbSet<T>> CreateMockDbSet<T>(List<T> data)
    where T : class
{
    var queryable = data.AsQueryable();
    var mockSet = new Mock<DbSet<T>>();

    mockSet.As<IQueryable<T>>()
        .Setup(m => m.Provider)
        .Returns(queryable.Provider);

    mockSet.As<IQueryable<T>>()
        .Setup(m => m.Expression)
        .Returns(queryable.Expression);

    return mockSet;
}

// Usage
var games = new List<Game> { new Game { Name = "Fortnite" } };
var mockDbSet = MockHelpers.CreateMockDbSet(games);
```

---

## End of Day 2 - Assessment

### Self-Check Questions

1. **Why do we use mocks in unit tests?**
   - Answer: To isolate the code being tested from external dependencies

2. **What's the difference between .Setup() and .Verify()?**
   - Answer: Setup configures mock behavior; Verify checks the mock was called correctly

3. **When should you use It.IsAny<T>()?**
   - Answer: When you don't care about the specific value passed to the mock

4. **What does Times.Once verify?**
   - Answer: That a method was called exactly one time

5. **What's the difference between .Returns() and .ReturnsAsync()?**
   - Answer: Returns is for synchronous methods; ReturnsAsync is for async methods

### Practical Exercises

Complete these before moving to Day 3:

#### Exercise 1: Mock a Repository (3 tests)
1. Test successful data retrieval
2. Test null/not found scenario
3. Test exception thrown

#### Exercise 2: Verify Mock Calls (2 tests)
1. Verify method called with specific parameters
2. Verify method never called

#### Exercise 3: Multiple Dependencies (1 test)
- Test a service with at least 3 mocked dependencies

---

### Homework

#### Reading Assignments

1. **Read:** `Fujiq.Test/Documentation/Design_Patterns_And_Mocking_Guide.md`
2. **Study:** `Fujiq.Test/Common/MockHelpers.cs`
3. **Review:** `Fujiq.Test/Services/GamesMasterAppServiceTests.cs` (lines 1-200)

#### Practice Assignments

1. Write 3 tests that mock a repository
2. Write 2 tests that verify mock calls using different Times options
3. Create a service with 3 dependencies and write tests for it

#### Preparation for Day 3

Tomorrow you'll learn:
- FluentAssertions for better test readability
- AutoFixture for automatic test data generation
- Fujiq.Test's MockDataBuilders

---

### Key Takeaways

- Mocks simulate dependencies without real infrastructure
- Use `new Mock<IInterface>()` to create mocks
- Use `.Setup()` to configure behavior
- Use `.Verify()` to check calls
- Use `.Object` to get the mock instance
- Use `It.IsAny<T>()` for flexible matching
- Use `It.Is<T>()` for condition-based matching
- Always verify important mock calls

---

**Next:** [Day 3: FluentAssertions, AutoFixture & Test Data](./Day-3-FluentAssertions-AutoFixture.md)
