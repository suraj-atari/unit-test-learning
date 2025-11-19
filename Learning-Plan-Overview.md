# 5-Day Unit Testing Learning Plan for .NET Developers
## Fujiq.Test Project - Comprehensive Training Guide

> **Target Audience:** .NET Developer with 4 years experience and strong C# knowledge
> **Framework:** xUnit with Moq, FluentAssertions, and AutoFixture
> **Goal:** Master unit testing concepts and the Fujiq.Test framework setup

---

## Learning Objectives

By the end of this 5-day program, you will:
- Understand core unit testing principles and the testing pyramid
- Write effective unit tests using xUnit framework
- Create and use mocks with Moq
- Apply the AAA (Arrange-Act-Assert) pattern consistently
- Use AutoFixture for test data generation
- Understand and measure test coverage
- Follow Fujiq.Test project conventions and best practices
- Write maintainable, readable tests using FluentAssertions

---

## Daily Schedule

### [Day 1: Unit Testing Fundamentals & xUnit Basics](./Day-1-Unit-Testing-Fundamentals.md)
**Duration:** 4-6 hours
- What is Unit Testing?
- The Testing Pyramid
- Introduction to xUnit
- The AAA Pattern (Arrange-Act-Assert)
- Writing your first tests
- Test naming conventions

**Deliverables:**
- 5 unit tests using [Fact]
- 3 parametrized tests using [Theory]
- All tests following AAA pattern

---

### [Day 2: Mocking with Moq & Test Isolation](./Day-2-Mocking-With-Moq.md)
**Duration:** 4-6 hours
- Why Mocking?
- Introduction to Moq
- Mock Setup Patterns
- Mock Verification
- Real-world examples from Fujiq.Test
- Testing with multiple dependencies

**Deliverables:**
- 3 tests that mock a repository
- 2 tests that verify mock calls
- 1 test with multiple mocked dependencies

---

### [Day 3: FluentAssertions, AutoFixture & Test Data](./Day-3-FluentAssertions-AutoFixture.md)
**Duration:** 4-6 hours
- FluentAssertions - Readable Assertions
- Introduction to AutoFixture
- AutoFixture Customization
- Fujiq.Test MockDataBuilders
- Complete test examples with real builders

**Deliverables:**
- 5 tests rewritten using FluentAssertions
- 3 tests using MockDataBuilders
- Understanding of test data generation patterns

---

### [Day 4: Test Coverage, Integration Tests & Best Practices](./Day-4-Test-Coverage-Integration-Tests.md)
**Duration:** 4-6 hours
- Understanding Test Coverage
- Types of Coverage (Line, Branch, Method)
- Running Coverage in Fujiq.Test
- What to Test (and What NOT to Test)
- Writing Testable Code
- Integration Tests
- TestBase Pattern

**Deliverables:**
- Coverage report for a module
- 2 integration tests using in-memory database
- Identification of 3 code sections with low coverage

---

### [Day 5: Advanced Patterns & Real-World Practice](./Day-5-Advanced-Patterns-Practice.md)
**Duration:** 4-6 hours
- Test Organization
- Testing Async Code
- Testing Exceptions
- Parametrized Tests with Complex Data
- Real-world test case study
- Common testing mistakes to avoid
- Final hands-on project

**Deliverables:**
- Complete test suite for a service (20+ tests)
- >80% code coverage
- Both unit and integration tests
- Following all Fujiq.Test conventions

---

## Prerequisites

### Software Setup
- Visual Studio 2022 or VS Code
- .NET 7.0 SDK
- Fujiq.Test project cloned and building successfully

### Required Knowledge
- Strong C# fundamentals
- Understanding of interfaces and dependency injection
- Familiarity with async/await
- Basic LINQ knowledge

---

## Testing Tools Used in Fujiq.Test

| Tool | Version | Purpose |
|------|---------|---------|
| xUnit | 2.4.2 | Testing framework |
| Moq | 4.20.69 | Mocking framework |
| FluentAssertions | 6.12.0 | Assertion library |
| AutoFixture | 4.18.0 | Test data generation |
| coverlet.collector | 3.2.0 | Code coverage |
| EF Core InMemory | 7.0.10 | In-memory database for testing |
| MockQueryable.Moq | 7.0.0 | Mock IQueryable and DbSet |

---

## Daily Study Plan

### Morning Session (2-3 hours)
- Read the day's concepts
- Review code examples
- Study Fujiq.Test reference files

### Afternoon Session (2-3 hours)
- Complete hands-on exercises
- Write practice tests
- Review and refactor

### Homework (1-2 hours)
- Read recommended documentation
- Study reference implementations
- Prepare for next day

---

## Assessment Criteria

### Day 1-2: Fundamentals
- [ ] Can explain unit testing principles
- [ ] Can write tests using xUnit
- [ ] Can apply AAA pattern consistently
- [ ] Can create and use mocks with Moq
- [ ] Can write descriptive test names

### Day 3-4: Intermediate
- [ ] Can use FluentAssertions effectively
- [ ] Can generate test data with AutoFixture
- [ ] Can use Fujiq MockDataBuilders
- [ ] Can run and interpret coverage reports
- [ ] Can write integration tests

### Day 5: Advanced
- [ ] Can organize tests effectively
- [ ] Can test async code
- [ ] Can test exceptions
- [ ] Can avoid common testing mistakes
- [ ] Can write comprehensive test suites

---

## Checklist for Writing Good Tests

Use this checklist for every test you write:

- [ ] Test name describes method, scenario, and expected result
- [ ] Test follows AAA pattern with clear sections
- [ ] Test has a single, clear purpose
- [ ] Dependencies are mocked (for unit tests)
- [ ] Test data is created using MockDataBuilders or AutoFixture
- [ ] Assertions use FluentAssertions
- [ ] Mock calls are verified (if applicable)
- [ ] Test is fast (< 100ms for unit tests)
- [ ] Test is isolated (doesn't depend on other tests)
- [ ] Edge cases are covered
- [ ] Test is readable and maintainable

---

## Fujiq.Test Documentation References

### Must Read
1. `Fujiq.Test/Documentation/Testing_Strategy_Guide.md` - Overall testing strategy
2. `Fujiq.Test/Documentation/README_MockData.md` - Mock data system
3. `Fujiq.Test/Documentation/Design_Patterns_And_Mocking_Guide.md` - Design patterns

### Study These Files
1. `Fujiq.Test/Common/TestBase.cs` - Base test class
2. `Fujiq.Test/Common/MockHelpers.cs` - Helper utilities
3. `Fujiq.Test/Common/MockDataBuilders.cs` - Data builders
4. `Fujiq.Test/Services/GamesMasterAppServiceTests.cs` - Comprehensive example
5. `Fujiq.Test/Integration/FujiqDomainIntegrationTests.cs` - Integration test example
6. `Fujiq.Test/Examples/MockDataUsageExamples.cs` - Usage examples

---

## External Resources

### Official Documentation
- [xUnit Documentation](https://xunit.net/)
- [Moq Documentation](https://github.com/moq/moq4)
- [FluentAssertions Documentation](https://fluentassertions.com/)
- [AutoFixture Documentation](https://github.com/AutoFixture/AutoFixture)

### Recommended Reading
- [Martin Fowler - Test Pyramid](https://martinfowler.com/bliki/TestPyramid.html)
- [Martin Fowler - Unit Testing](https://martinfowler.com/bliki/UnitTest.html)
- [Roy Osherove - The Art of Unit Testing](https://www.artofunittesting.com/)

---

## Progress Tracking

Track your progress using the Excel sheet: `Unit-Testing-Progress-Tracker.xlsx`

| Day | Completion | Tests Written | Coverage % | Notes |
|-----|------------|---------------|------------|-------|
| Day 1 | ☐ | | | |
| Day 2 | ☐ | | | |
| Day 3 | ☐ | | | |
| Day 4 | ☐ | | | |
| Day 5 | ☐ | | | |

---

## Getting Help

If you get stuck:
1. Review the day's material again
2. Study the referenced Fujiq.Test files
3. Check the official documentation
4. Ask questions during code review sessions
5. Pair program with experienced team members

---

## Next Steps After Completion

1. Start writing tests for new features
2. Add tests to existing code (refactoring opportunity)
3. Review and improve existing tests
4. Share knowledge with team members
5. Continue learning advanced testing patterns
6. Explore test-driven development (TDD)
7. Learn about behavior-driven development (BDD)

---

## Best Practices Summary

### DO:
- Write tests before fixing bugs
- Keep tests simple and focused
- Use descriptive test names
- Follow AAA pattern
- Use FluentAssertions for readability
- Mock external dependencies
- Test both success and failure scenarios
- Test edge cases and boundaries
- Keep tests independent
- Run tests frequently during development

### DON'T:
- Don't test framework code
- Don't test private methods directly
- Don't write tests that depend on test execution order
- Don't test multiple things in one test
- Don't over-mock (integration tests need real dependencies)
- Don't ignore failing tests
- Don't write tests just to increase coverage
- Don't test implementation details
- Don't use Thread.Sleep in tests
- Don't commit commented-out tests

---

**Ready to start? Begin with [Day 1: Unit Testing Fundamentals & xUnit Basics](./Day-1-Unit-Testing-Fundamentals.md)**
