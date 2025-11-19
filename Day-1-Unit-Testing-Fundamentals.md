# Day 1: Unit Testing Fundamentals & xUnit Basics

**Duration:** 4-6 hours
**Prerequisites:** C# fundamentals, understanding of interfaces

---

## Learning Objectives

By the end of Day 1, you will:
- Understand what unit testing is and why it matters
- Know the testing pyramid and test distribution
- Be able to write basic tests using xUnit
- Apply the AAA (Arrange-Act-Assert) pattern
- Follow proper test naming conventions
- Write both simple and parametrized tests

---

## Morning Session (2-3 hours)

### 1.1 What is Unit Testing?

**Definition:** A unit test is an automated test that:
- Tests a single unit of code in isolation (usually one method/class)
- Has no dependencies on external systems (database, file system, network)
- Runs fast (milliseconds)
- Is repeatable and deterministic

**Example:**
```csharp
// Code to test
public class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }
}

// Unit test
[Fact]
public void Add_TwoNumbers_ReturnsSum()
{
    // Arrange
    var calculator = new Calculator();

    // Act
    var result = calculator.Add(5, 3);

    // Assert
    Assert.Equal(8, result);
}
```

### Why Unit Testing Matters

#### 1. Early Bug Detection
- Catch issues before they reach production
- Find bugs when they're cheapest to fix
- Reduce debugging time

#### 2. Living Documentation
- Tests show how code should work
- New developers can read tests to understand functionality
- Tests document edge cases and requirements

#### 3. Refactoring Safety Net
- Change code confidently
- Tests catch regressions immediately
- Enables continuous improvement

#### 4. Better Design
- Testable code is usually well-designed code
- Forces you to think about dependencies
- Encourages SOLID principles

#### 5. Faster Development (Long-term)
- Initial investment pays off quickly
- Less time debugging
- Faster feature development
- Reduced production bugs

---

### 1.2 The Testing Pyramid

```
        /\
       /  \      E2E Tests (Few, Slow, Expensive)
      /____\     - Full application tests
     /      \    - Browser automation
    / Integ. \   Integration Tests (Some, Medium Speed)
   /__________\  - Database interactions
  /            \ - Multiple components together
 /   Unit Tests \ Unit Tests (Many, Fast, Cheap)
/________________\ - Single method/class
                   - Mocked dependencies
```

#### Distribution in Fujiq.Test

| Test Type | Percentage | Count | Speed | Purpose |
|-----------|------------|-------|-------|---------|
| **Unit Tests** | ~70% | 38+ files | Milliseconds | Fast feedback |
| **Integration Tests** | ~20% | 5+ files | Seconds | Confidence in integration |
| **Controller/API Tests** | ~10% | 4+ files | Seconds | End-to-end validation |

**Key Principle:** Write more unit tests, fewer integration tests, even fewer E2E tests

#### Why This Distribution?

**Unit Tests (70%):**
- Fast: Run in milliseconds
- Cheap: Easy to write and maintain
- Specific: Pinpoint exact failures
- Many: Cover all logic branches

**Integration Tests (20%):**
- Medium Speed: Run in seconds
- Confidence: Test components work together
- Database: Test real Entity Framework behavior
- Workflows: Test complete scenarios

**E2E Tests (10%):**
- Slow: Run in seconds/minutes
- Expensive: Complex setup and maintenance
- Broad: Test entire user flows
- Few: Only critical paths

---

### 1.3 Introduction to xUnit

xUnit is the testing framework used in Fujiq.Test. It's the modern successor to NUnit.

#### Why xUnit?

- Modern design (created by the inventor of NUnit)
- Excellent async support
- Extensible
- Great Visual Studio integration
- Industry standard for .NET

#### Key Attributes

**[Fact] - Simple Test:**
```csharp
[Fact]
public void MyFirstTest()
{
    // Test implementation
    var result = 2 + 2;
    Assert.Equal(4, result);
}
```

**[Theory] - Parametrized Test:**
```csharp
[Theory]
[InlineData(1, 2, 3)]
[InlineData(5, 5, 10)]
[InlineData(-1, 1, 0)]
public void Add_DifferentNumbers_ReturnsCorrectSum(int a, int b, int expected)
{
    // Test implementation with parameters
    var calculator = new Calculator();
    var result = calculator.Add(a, b);
    Assert.Equal(expected, result);
}
```

#### Test Discovery & Execution

**Run all tests:**
```bash
dotnet test
```

**Run tests in a specific file:**
```bash
dotnet test --filter "FullyQualifiedName~GamesMasterAppServiceTests"
```

**Run tests by category:**
```bash
dotnet test --filter Category=Unit
```

**Run a single test:**
```bash
dotnet test --filter "FullyQualifiedName~Add_TwoNumbers_ReturnsSum"
```

**Run with detailed output:**
```bash
dotnet test --logger "console;verbosity=detailed"
```

---

### 1.4 The AAA Pattern (Arrange-Act-Assert)

This is the **most important** pattern in unit testing.

#### The Pattern

```csharp
[Fact]
public void Add_TwoNumbers_ReturnsSum()
{
    // ARRANGE - Set up test data and dependencies
    var calculator = new Calculator();
    int a = 5;
    int b = 3;

    // ACT - Execute the method being tested
    var result = calculator.Add(a, b);

    // ASSERT - Verify the result
    Assert.Equal(8, result);
}
```

#### 1. Arrange Phase

**Purpose:** Set up everything needed for the test

```csharp
// Arrange
var calculator = new Calculator();
int firstNumber = 5;
int secondNumber = 3;
int expectedResult = 8;
```

**What to do in Arrange:**
- Create objects
- Set up test data
- Configure mocks (Day 2)
- Prepare dependencies

#### 2. Act Phase

**Purpose:** Execute the code being tested

```csharp
// Act
var result = calculator.Add(firstNumber, secondNumber);
```

**Important:**
- Usually just ONE line
- Call the method you're testing
- Capture the result

#### 3. Assert Phase

**Purpose:** Verify the result matches expectations

```csharp
// Assert
Assert.Equal(expectedResult, result);
```

**What to verify:**
- Return values
- Object state
- Method calls (with mocks)
- Exceptions thrown

#### Why AAA?

- **Clear separation** of concerns
- **Easy to read** and understand
- **Consistent structure** across all tests
- **Easier debugging** when tests fail
- **Team standard** - everyone writes tests the same way

#### Common Variations

**Given-When-Then (BDD Style):**
```csharp
[Fact]
public void Add_TwoNumbers_ReturnsSum()
{
    // Given: I have a calculator and two numbers
    var calculator = new Calculator();
    int a = 5, b = 3;

    // When: I add them together
    var result = calculator.Add(a, b);

    // Then: I should get the sum
    Assert.Equal(8, result);
}
```

Both patterns are equivalent - AAA is more common in .NET.

---

## Afternoon Session (2-3 hours)

### 1.5 Your First Test - Hands-On Exercise

#### Exercise 1.1: Write a Basic Unit Test

Create a new test file: `Exercises/Day1_BasicTests.cs`

```csharp
using Xunit;

namespace Fujiq.Test.Exercises
{
    public class Day1_BasicTests
    {
        #region Basic String Tests

        [Fact]
        public void String_Concatenation_Should_Work()
        {
            // Arrange
            string firstName = "John";
            string lastName = "Doe";

            // Act
            string fullName = $"{firstName} {lastName}";

            // Assert
            Assert.Equal("John Doe", fullName);
        }

        [Fact]
        public void String_ToUpper_Should_ReturnUppercase()
        {
            // Arrange
            string input = "hello";

            // Act
            string result = input.ToUpper();

            // Assert
            Assert.Equal("HELLO", result);
        }

        #endregion

        #region Math Helper Tests

        [Theory]
        [InlineData(0, true)]
        [InlineData(2, true)]
        [InlineData(4, true)]
        [InlineData(1, false)]
        [InlineData(3, false)]
        [InlineData(-2, true)]
        public void IsEven_Should_Return_Correct_Result(int number, bool expected)
        {
            // Arrange
            var mathHelper = new MathHelper();

            // Act
            var result = mathHelper.IsEven(number);

            // Assert
            Assert.Equal(expected, result);
        }

        [Theory]
        [InlineData(5, 3, 8)]
        [InlineData(0, 0, 0)]
        [InlineData(-5, 5, 0)]
        [InlineData(-5, -3, -8)]
        public void Add_Should_Return_Correct_Sum(int a, int b, int expected)
        {
            // Arrange
            var mathHelper = new MathHelper();

            // Act
            var result = mathHelper.Add(a, b);

            // Assert
            Assert.Equal(expected, result);
        }

        #endregion

        #region Null Handling Tests

        [Fact]
        public void GetUser_WhenUserNotFound_ShouldReturnNull()
        {
            // Arrange
            var userService = new SimpleUserService();

            // Act
            var result = userService.GetUser(Guid.NewGuid());

            // Assert
            Assert.Null(result);
        }

        [Fact]
        public void GetUser_WhenUserExists_ShouldReturnUser()
        {
            // Arrange
            var userService = new SimpleUserService();
            var userId = Guid.NewGuid();
            userService.AddUser(userId, "John Doe");

            // Act
            var result = userService.GetUser(userId);

            // Assert
            Assert.NotNull(result);
            Assert.Equal("John Doe", result);
        }

        #endregion
    }

    #region Simple Classes to Test

    public class MathHelper
    {
        public bool IsEven(int number) => number % 2 == 0;

        public int Add(int a, int b) => a + b;

        public int Multiply(int a, int b) => a * b;
    }

    public class SimpleUserService
    {
        private readonly Dictionary<Guid, string> _users = new();

        public void AddUser(Guid id, string name)
        {
            _users[id] = name;
        }

        public string? GetUser(Guid id)
        {
            return _users.TryGetValue(id, out var name) ? name : null;
        }
    }

    #endregion
}
```

#### Exercise 1.2: Run Your Tests

```bash
# Navigate to test project
cd Fujiq.Test

# Run all tests
dotnet test

# Run only your new tests
dotnet test --filter "FullyQualifiedName~Day1_BasicTests"
```

#### Exercise 1.3: Add More Tests

Add these tests yourself:

1. Test `Multiply` method in `MathHelper`
2. Test edge cases (multiplying by 0, by 1, by -1)
3. Add a `Subtract` method and test it
4. Test string operations (Contains, StartsWith, EndsWith)

---

### 1.6 Test Naming Conventions

Good test names are crucial for understanding what failed when a test breaks.

#### Fujiq.Test Standard Convention

```
MethodName_Scenario_ExpectedResult
```

**Examples:**
```csharp
GetByIdAsync_ShouldReturnGame_WhenGameExists
GetByIdAsync_ShouldReturnNull_WhenGameDoesNotExist
Add_ShouldThrowException_WhenInputIsNull
FindAsync_ShouldReturnMatchingGames_WhenFilterIsApplied
DeleteAsync_ById_ShouldCallRepository_AndSaveChanges
```

#### Breaking Down the Pattern

**1. MethodName:** What method are you testing?
```csharp
GetByIdAsync_...
Add_...
FindAsync_...
```

**2. Scenario:** Under what conditions?
```csharp
..._WhenGameExists
..._WhenInputIsNull
..._WhenFilterIsApplied
```

**3. ExpectedResult:** What should happen?
```csharp
..._ShouldReturnGame
..._ShouldReturnNull
..._ShouldThrowException
```

#### Alternative Convention (BDD Style)

```
When_I_Action_Condition_ItShould_Result
```

**Examples:**
```csharp
When_I_Call_GetById_With_ValidId_ItShould_ReturnUser
When_I_Delete_NonExistentUser_ItShould_ThrowException
When_I_Call_Active_Users_With_Real_Data_ItShouldReturnActiveUsers
```

#### Good vs Bad Test Names

**Bad Names:**
```csharp
Test1()                    // What does this test?
TestGetUser()              // What scenario? What result?
UserTest()                 // Too vague
GetByIdWorks()            // Works in what scenario?
```

**Good Names:**
```csharp
GetById_ShouldReturnUser_WhenUserExists()
GetById_ShouldReturnNull_WhenUserDoesNotExist()
GetById_ShouldThrowException_WhenIdIsEmpty()
```

#### Key Rules

1. **Describe WHAT** is being tested (method name)
2. **Describe the SCENARIO** (condition/context)
3. **Describe the EXPECTED** result
4. **Use underscores** for readability
5. **Be descriptive** - long names are fine!
6. **Avoid abbreviations** unless they're well-known
7. **Be consistent** across your test suite

---

### 1.7 Common xUnit Assertions

#### Equality Assertions

```csharp
// Basic equality
Assert.Equal(expected, actual);
Assert.NotEqual(expected, actual);

// Reference equality
Assert.Same(expected, actual);
Assert.NotSame(expected, actual);
```

#### Null Assertions

```csharp
Assert.Null(value);
Assert.NotNull(value);
```

#### Boolean Assertions

```csharp
Assert.True(condition);
Assert.False(condition);
```

#### Collection Assertions

```csharp
// Check if collection contains an item
Assert.Contains(expectedItem, collection);
Assert.DoesNotContain(expectedItem, collection);

// Check collection count
Assert.Empty(collection);
Assert.NotEmpty(collection);
Assert.Single(collection);  // Exactly one item

// Check all items match a condition
Assert.All(collection, item => Assert.NotNull(item));
```

#### Exception Assertions

```csharp
// Synchronous
var exception = Assert.Throws<ArgumentException>(() =>
{
    MethodThatThrows();
});
Assert.Equal("Expected message", exception.Message);

// Async
await Assert.ThrowsAsync<ArgumentException>(async () =>
{
    await AsyncMethodThatThrows();
});
```

#### Type Assertions

```csharp
Assert.IsType<ExpectedType>(obj);
Assert.IsNotType<UnexpectedType>(obj);
Assert.IsAssignableFrom<BaseType>(obj);
```

---

## End of Day 1 - Assessment

### Self-Check Questions

1. **What are the three parts of the AAA pattern?**
   - Answer: Arrange, Act, Assert

2. **What's the difference between [Fact] and [Theory]?**
   - Answer: [Fact] is for simple tests with no parameters, [Theory] is for parametrized tests with [InlineData]

3. **Why do we write more unit tests than integration tests?**
   - Answer: Unit tests are faster, cheaper, easier to maintain, and provide quick feedback

4. **What makes a good test name?**
   - Answer: Describes method, scenario, and expected result clearly

5. **What is a unit test?**
   - Answer: An automated test that tests a single unit of code in isolation, runs fast, and is repeatable

### Practical Exercises

Complete these before moving to Day 2:

#### Exercise 1: Write 5 Unit Tests Using [Fact]

Create tests for:
1. String manipulation (concatenation, trimming, etc.)
2. Math operations (add, subtract, multiply)
3. Boolean logic (AND, OR, NOT)
4. Null handling
5. Object property assignment

#### Exercise 2: Write 3 Parametrized Tests Using [Theory]

Use [InlineData] to test:
1. Multiple math scenarios
2. String validation (email, phone, etc.)
3. Date calculations

#### Exercise 3: Practice AAA Pattern

Review each test you wrote and ensure:
- Clear Arrange section
- Single Act line
- Appropriate Assert statements
- Comments separating sections

---

### Homework

#### Reading Assignments

1. **Read:** `Fujiq.Test/Documentation/Testing_Strategy_Guide.md`
   - Focus on: Testing pyramid section
   - Focus on: Unit test best practices

2. **Study:** `Fujiq.Test/Services/GamesMasterAppServiceTests.cs`
   - Look at test organization
   - Note the naming conventions
   - Observe the AAA pattern usage
   - See how tests are grouped with #region

#### Practice Assignments

1. **Write 5 more [Fact] tests** for different scenarios
2. **Write 3 more [Theory] tests** with at least 3 [InlineData] each
3. **Refactor any old tests** to follow AAA pattern strictly

#### Preparation for Day 2

Think about these questions:
- How would you test a method that depends on a database?
- How would you test a method that sends an email?
- How would you test a method that calls an external API?

**Hint:** You can't use real databases, email servers, or APIs in unit tests. Tomorrow you'll learn about **Mocking**!

---

### Key Takeaways

- Unit tests test single units of code in isolation
- Follow the testing pyramid: many unit tests, fewer integration tests
- xUnit uses [Fact] for simple tests and [Theory] for parametrized tests
- **Always** use the AAA pattern: Arrange, Act, Assert
- Good test names describe method, scenario, and expected result
- Tests should be fast, isolated, and repeatable

---

### Reference Files to Study

Before Day 2, review these files in Fujiq.Test:

1. `Fujiq.Test/Services/GamesMasterAppServiceTests.cs:1-100`
   - See how professional tests are structured
   - Note the constructor setup
   - Observe test organization

2. `Fujiq.Test/Common/TestBase.cs`
   - Understand the base class pattern
   - See common test infrastructure

3. Any simple test file to get comfortable with the patterns

---

**Next:** [Day 2: Mocking with Moq & Test Isolation](./Day-2-Mocking-With-Moq.md)
