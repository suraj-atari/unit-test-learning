# Unit Testing Learning Program
## Comprehensive Training for Fujiq.Test Framework

Welcome to the Fujiq.Test Unit Testing Learning Program! This comprehensive training curriculum is designed to help developers master unit testing concepts and the specific framework setup used in the Fujiq.Test project.

---

## 📚 Overview

This learning program provides:
- **5-day structured curriculum** from fundamentals to advanced patterns
- **Hands-on exercises** based on real Fujiq.Test code
- **Progress tracking tools** to monitor learning
- **AI-powered analyzer** for personalized learning paths
- **Real-world examples** from the actual project

**Target Audience:** .NET developers with 4+ years of experience and strong C# knowledge

**Estimated Time:** 20-30 hours over 5 days

---

## 📁 Materials Included

### Learning Curriculum

1. **[Learning Plan Overview](./Learning-Plan-Overview.md)**
   - Program objectives and structure
   - Daily schedule and deliverables
   - Assessment criteria
   - Resource references

2. **[Day 1: Unit Testing Fundamentals](./Day-1-Unit-Testing-Fundamentals.md)**
   - What is unit testing and why it matters
   - The testing pyramid
   - xUnit framework basics
   - AAA (Arrange-Act-Assert) pattern
   - Test naming conventions
   - **Exercises:** 8+ hands-on exercises

3. **[Day 2: Mocking with Moq](./Day-2-Mocking-With-Moq.md)**
   - Why mocking is essential
   - Moq framework fundamentals
   - Mock setup and verification patterns
   - Testing with multiple dependencies
   - Real-world examples from Fujiq.Test
   - **Exercises:** 6+ practical exercises

4. **[Day 3: FluentAssertions & Test Data](./Day-3-FluentAssertions-AutoFixture.md)**
   - Readable assertions with FluentAssertions
   - AutoFixture for test data generation
   - Fujiq.Test MockDataBuilders
   - Test data strategies
   - Complete test scenarios
   - **Exercises:** 5+ exercises

5. **[Day 4: Test Coverage & Integration Tests](./Day-4-Test-Coverage-Integration-Tests.md)**
   - Understanding code coverage types
   - Running and interpreting coverage reports
   - What to test (and what NOT to test)
   - Writing testable code
   - Integration testing with in-memory databases
   - TestBase pattern
   - **Exercises:** 4+ exercises

6. **[Day 5: Advanced Patterns & Practice](./Day-5-Advanced-Patterns-Practice.md)**
   - Test organization best practices
   - Testing async code
   - Testing exceptions
   - Parametrized tests with complex data
   - Common testing mistakes to avoid
   - Final hands-on project
   - **Exercises:** Final project (20+ tests)

### Progress Tracking

7. **[Progress Tracker (CSV/Excel)](./Unit-Test-Learning-Progress-Tracker.csv)**
   - Daily progress tracking
   - Exercise completion checklist
   - Coverage metrics
   - Skills assessment matrix
   - Mentor feedback log
   - Certification checklist

### AI-Powered Analysis

8. **[AI Test Analyzer Agent](./AI-Test-Analyzer-Agent.md)** (Specification)
   - Automated project analysis concept
   - Learning gap identification
   - Personalized plan generation theory
   - Custom exercise creation ideas
   - Implementation guide

9. **[Test Analyzer Tool](./TestAnalyzer/)** ⭐ **ACTUAL WORKING TOOL**
   - **Executable AI agent** that analyzes your project
   - **Automatically creates test projects** if none exist
   - **Generates customized learning plans** based on your architecture
   - **Adapts to your tech stack** (xUnit/Moq/FluentAssertions)
   - **Interactive prompts** for days and skill level
   - See **[How to Use Guide](./HOW-TO-USE-TEST-ANALYZER.md)** for details

---

## 🚀 Getting Started

### For Learners

1. **Review Prerequisites**
   - Ensure you have .NET 7.0 SDK installed
   - Clone the Fujiq.Test project
   - Verify you can build and run tests

2. **Start with Day 1**
   - Read [Learning Plan Overview](./Learning-Plan-Overview.md)
   - Begin with [Day 1: Fundamentals](./Day-1-Unit-Testing-Fundamentals.md)
   - Complete all exercises before moving to Day 2

3. **Track Your Progress**
   - Open [Progress Tracker CSV](./Unit-Test-Learning-Progress-Tracker.csv) in Excel
   - Update your status after each module
   - Record tests written and coverage achieved

4. **Follow the Daily Pattern**
   - **Morning:** Read concepts (2-3 hours)
   - **Afternoon:** Complete exercises (2-3 hours)
   - **Evening:** Homework and review (1-2 hours)

5. **Complete Final Project**
   - Day 5 includes a comprehensive final project
   - Write a complete test suite (20+ tests)
   - Achieve >80% code coverage
   - Apply all learned concepts

### For Mentors

1. **Review the Learning Plan**
   - Understand the 5-day curriculum structure
   - Review exercises and expected outcomes
   - Prepare to answer questions

2. **Set Up Progress Tracking**
   - Share the [Progress Tracker](./Unit-Test-Learning-Progress-Tracker.csv) with learner
   - Schedule daily check-ins (30 minutes)
   - Review completed exercises

3. **Provide Guidance**
   - Review learner's code daily
   - Provide constructive feedback
   - Share additional resources as needed

4. **Assess Progress**
   - Use the assessment sections in each day
   - Review quiz answers
   - Evaluate final project against checklist

---

## 📊 Learning Path

```
Day 1: Fundamentals
└── Build foundation in unit testing and xUnit

Day 2: Mocking
└── Learn to isolate code with Moq

Day 3: Better Tests
└── Write readable, maintainable tests

Day 4: Coverage & Integration
└── Understand quality metrics and integration testing

Day 5: Mastery
└── Apply all concepts to real-world scenarios
```

---

## 🎯 Learning Objectives

By completing this program, you will be able to:

- [ ] Explain unit testing principles and the testing pyramid
- [ ] Write effective unit tests using xUnit
- [ ] Create and use mocks with Moq
- [ ] Apply the AAA pattern consistently
- [ ] Write readable assertions with FluentAssertions
- [ ] Generate test data efficiently with AutoFixture
- [ ] Use Fujiq.Test MockDataBuilders
- [ ] Understand and measure code coverage
- [ ] Write integration tests with in-memory databases
- [ ] Organize tests effectively
- [ ] Test async code correctly
- [ ] Avoid common testing mistakes
- [ ] Write comprehensive test suites independently

---

## 📈 Assessment & Certification

### Daily Assessments
- **Self-check questions** at the end of each day
- **Practical exercises** to demonstrate understanding
- **Homework assignments** for reinforcement

### Final Assessment
- **Comprehensive quiz** (10 questions, need 8/10 to pass)
- **Final project** (complete test suite with 20+ tests)
- **Code review** with mentor
- **Coverage goal** (>80% on final project)

### Certification Checklist
- [ ] Completed all 5 days of training
- [ ] Completed all exercises and homework
- [ ] Written final project (20+ tests)
- [ ] Achieved >80% coverage on project
- [ ] Passed final quiz (8/10 or better)
- [ ] Demonstrated understanding of core concepts
- [ ] Can write tests independently
- [ ] Code review passed

---

## 🛠️ Tools & Technologies

This program covers the following testing stack:

| Tool | Version | Purpose |
|------|---------|---------|
| xUnit | 2.4.2 | Testing framework |
| Moq | 4.20.69 | Mocking framework |
| FluentAssertions | 6.12.0 | Assertion library |
| AutoFixture | 4.18.0 | Test data generation |
| coverlet.collector | 3.2.0 | Code coverage tool |
| EF Core InMemory | 7.0.10 | In-memory database |
| MockQueryable.Moq | 7.0.0 | Mock IQueryable/DbSet |

---

## 📖 Reference Materials

### Internal Documentation
Located in `Fujiq.Test/Documentation/`:
- `Testing_Strategy_Guide.md` - Overall testing strategy
- `README_MockData.md` - Mock data system guide
- `Design_Patterns_And_Mocking_Guide.md` - Patterns and practices
- `AutoFixture_Complete_Guide.md` - AutoFixture usage
- `Scraper_Testing_Strategy_Guide.md` - Specialized testing

### Key Files to Study
Located in `Fujiq.Test/`:
- `Common/TestBase.cs` - Base test class
- `Common/MockHelpers.cs` - Helper utilities
- `Common/MockDataBuilders.cs` - Data builders (600+ lines)
- `Services/GamesMasterAppServiceTests.cs` - Comprehensive example (965 lines)
- `Integration/FujiqDomainIntegrationTests.cs` - Integration example
- `Examples/MockDataUsageExamples.cs` - Usage patterns

### External Resources
- [xUnit Documentation](https://xunit.net/)
- [Moq Documentation](https://github.com/moq/moq4)
- [FluentAssertions Documentation](https://fluentassertions.com/)
- [AutoFixture Documentation](https://github.com/AutoFixture/AutoFixture)
- [Martin Fowler - Test Pyramid](https://martinfowler.com/bliki/TestPyramid.html)
- [The Art of Unit Testing](https://www.artofunittesting.com/) (Book)

---

## 💡 Best Practices Summary

### DO:
✅ Write tests before fixing bugs
✅ Keep tests simple and focused
✅ Use descriptive test names (MethodName_Scenario_ExpectedResult)
✅ Follow AAA pattern consistently
✅ Use FluentAssertions for readability
✅ Mock external dependencies
✅ Test both success and failure scenarios
✅ Test edge cases and boundaries
✅ Keep tests independent
✅ Run tests frequently during development

### DON'T:
❌ Don't test framework code
❌ Don't test private methods directly
❌ Don't write tests that depend on execution order
❌ Don't test multiple things in one test
❌ Don't over-mock (integration tests need real components)
❌ Don't ignore failing tests
❌ Don't write tests just to increase coverage
❌ Don't test implementation details
❌ Don't use Thread.Sleep in tests
❌ Don't commit commented-out tests

---

## 🔧 Troubleshooting

### Common Issues

**Issue: Tests fail when run all together but pass individually**
- **Cause:** Shared state between tests
- **Solution:** Ensure each test is isolated, use `IDisposable` for cleanup

**Issue: Can't mock a class**
- **Cause:** Trying to mock a concrete class
- **Solution:** Mock interfaces instead, or make methods virtual

**Issue: Coverage not generating**
- **Cause:** Missing coverlet.collector package
- **Solution:** Add package reference and rebuild

**Issue: In-memory database data persists between tests**
- **Cause:** Using same database name
- **Solution:** Use `Guid.NewGuid().ToString()` for database name

### Getting Help

1. **Review the day's material** again
2. **Check the referenced Fujiq.Test files** for examples
3. **Consult the official documentation**
4. **Ask your mentor or team** during code review
5. **Search Stack Overflow** for specific errors
6. **Pair program** with experienced developers

---

## 📅 Recommended Schedule

### Week 1: Intensive Learning

| Day | Focus | Time | Deliverables |
|-----|-------|------|--------------|
| **Monday** | Day 1: Fundamentals | 4-6 hours | 8 tests written |
| **Tuesday** | Day 2: Mocking | 4-6 hours | 5 tests with mocks |
| **Wednesday** | Day 3: Better Tests | 4-6 hours | 8 tests improved |
| **Thursday** | Day 4: Coverage | 4-6 hours | Coverage report + 2 integration tests |
| **Friday** | Day 5: Final Project | 4-6 hours | 20+ test suite |

### Week 2: Practice & Refinement
- Apply learned concepts to real work
- Review and improve existing tests
- Mentor junior developers

---

## 🎓 Next Steps After Completion

1. **Apply Skills Daily**
   - Write tests for all new features
   - Refactor existing tests
   - Increase project coverage

2. **Advanced Topics**
   - Test-Driven Development (TDD)
   - Behavior-Driven Development (BDD) with SpecFlow
   - Property-Based Testing with FsCheck
   - Performance testing with BenchmarkDotNet
   - Mutation testing with Stryker.NET

3. **Knowledge Sharing**
   - Present learnings to the team
   - Mentor other developers
   - Contribute to testing documentation
   - Share best practices

4. **Continuous Improvement**
   - Stay updated with testing tools and patterns
   - Participate in testing community
   - Read testing blogs and books
   - Attend testing conferences/webinars

---

## 📞 Support & Feedback

### Questions?
- Contact your assigned mentor
- Check the Fujiq.Test documentation
- Review Stack Overflow
- Ask in team chat

### Feedback
We value your feedback! After completing the program:
- Fill out the feedback form
- Suggest improvements
- Share what worked well
- Identify gaps in training

### Contributing
Found a mistake or have a suggestion?
1. Create an issue in the repository
2. Submit a pull request with improvements
3. Share additional resources
4. Create new exercises

---

## 📜 License & Credits

**Created by:** Development Team
**Date:** January 2025
**Version:** 1.0
**Based on:** Fujiq.Test project (45 test files, 800+ tests)

**Acknowledgments:**
- Fujiq.Test project contributors
- xUnit, Moq, FluentAssertions teams
- Testing community best practices

---

## 🎯 Quick Links

- [Start Day 1](./Day-1-Unit-Testing-Fundamentals.md)
- [Learning Plan Overview](./Learning-Plan-Overview.md)
- [Progress Tracker](./Unit-Test-Learning-Progress-Tracker.csv)
- [AI Analyzer Guide](./AI-Test-Analyzer-Agent.md)
- [Fujiq.Test Documentation](../../../Fujiq.Test/Documentation/)

---

**Ready to become a unit testing expert? Start with [Day 1](./Day-1-Unit-Testing-Fundamentals.md)!**

Good luck with your learning journey! 🚀
