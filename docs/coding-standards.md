# Coding Standards

This document defines the coding standards that developers and agents should follow when adding features to this solution. The goal is to keep the codebase consistent, readable, and aligned with the layered architecture described in the architecture guide.

## 1. Scope and Principles

These standards apply to all projects in the solution, including:
- Business.Service
- Business.Specifications
- Business.Models
- Business.Data
- Test projects under tests/

The default principles are:
- Keep the code simple and explicit
- Favor readability over cleverness
- Keep business logic out of the HTTP layer
- Preserve the separation between presentation, business, domain, and data concerns
- Prefer small, focused classes and methods

## 2. Project Structure and Placement

When adding a new feature, place code in the project that owns the responsibility:
- API endpoints and HTTP concerns: Business.Service
- Business workflows and use cases: Business.Specifications
- Domain entities, value objects, DTOs, and shared contracts: Business.Models
- Persistence and data access concerns: Business.Data
- Tests: the matching test project under tests/

Use a feature-oriented folder layout when a feature grows beyond a single file. A simple structure is:
- FeatureName/
  - Models/
  - Services/
  - Validators/
  - Tests/

## 3. Naming and Organization

Use consistent, descriptive names:
- Classes, methods, properties, and variables should use PascalCase for types and camelCase for locals
- Interfaces should start with I
- Async methods should end with Async
- Boolean properties should read like a statement, for example IsActive

Prefer file-scoped namespaces and keep namespace names aligned with the owning project.

Example:
```csharp
namespace Business.Specifications.Orders;
```

## 4. C# Style and Language Usage

Follow these conventions:
- Use C# 12+ features when the target framework supports them
- Prefer file-scoped namespaces
- Use records for simple immutable data transfer objects when appropriate
- Keep methods focused and avoid large monolithic implementations
- Avoid unnecessary abstractions; introduce interfaces only when they add clear value

Use null-safety features consistently:
- Enable nullable reference types in all projects
- Guard arguments early with ArgumentNullException.ThrowIfNull or string.IsNullOrWhiteSpace where appropriate

## 5. Public API and Documentation

All public classes, interfaces, methods, properties, and records should include XML documentation comments where they are part of the public surface.

Required XML comment content:
- Summary of the purpose
- Parameter descriptions for methods and constructors
- Return value description for methods returning values

Example:
```csharp
/// <summary>
/// Creates a new order service instance.
/// </summary>
/// <param name="repository">The repository used to persist orders.</param>
public sealed class OrderService(IOrderRepository repository)
{
}
```

Keep comments focused on intent and behavior. Avoid repeating what the code already says.

## 6. Dependency Injection and Service Design

Prefer constructor injection for dependencies.

Use the primary constructor syntax when it improves readability for services with a small number of dependencies:
```csharp
public sealed class OrderService(IOrderRepository repository)
{
    private readonly IOrderRepository _repository = repository;
}
```

Use these rules for service design:
- Constructor parameters should be validated with ArgumentNullException.ThrowIfNull
- Register services in the composition root in Business.Service
- Choose the appropriate lifetime:
  - Singleton for stateless shared services
  - Scoped for request-bound services
  - Transient for lightweight, short-lived services
- Keep infrastructure concerns outside the domain model

If a service is only used for testability and has no meaningful abstraction, avoid introducing an interface just for the sake of it.

## 7. Async and Task-Based Programming

Use async and await for I/O-bound and long-running work.

Follow these guidelines:
- Methods that perform I/O or wait for external work should return Task or Task<T>
- Async methods should end with Async
- Pass CancellationToken through the call chain when possible
- Use Task.Delay with a cancellation token when delays are required
- Avoid blocking calls like .Result or .Wait()

Example:
```csharp
public async Task<Order?> GetByIdAsync(int id, CancellationToken cancellationToken)
{
    ArgumentOutOfRangeException.ThrowIfNegativeOrZero(id);
    return await _repository.GetByIdAsync(id, cancellationToken);
}
```

## 8. Error Handling and Logging

Handle errors explicitly and avoid swallowing exceptions.

Use these practices:
- Throw specific exception types when invalid state or input is encountered
- Include meaningful messages that help diagnose the issue
- Keep error handling close to the boundary where the problem is detected
- Use structured logging with meaningful context once logging is introduced in a feature
- Do not catch general Exception unless there is a clear and justified reason

Prefer returning clear domain-specific failures or validation results over throwing for expected validation errors when appropriate.

## 9. Configuration and Settings

Configuration should be centralized and strongly typed where practical.

Use these conventions:
- Keep configuration values in appsettings.json or environment-specific appsettings files
- Prefer strongly typed options objects over repeated IConfiguration lookups
- Validate required settings explicitly
- Avoid hard-coding secrets or environment-specific values

## 10. Validation and Business Rules

Validation should happen at the appropriate boundary:
- API input validation at the service boundary
- Business rule validation in the business layer
- Domain constraints should be enforced where the domain concept is defined

Keep validation logic clear and predictable. Avoid mixing validation rules with unrelated concerns in the same method.

## 11. Testing Standards

The solution already uses NUnit for tests, so new tests should follow that framework.

Testing expectations:
- Use the Arrange-Act-Assert pattern
- Write one behavior-focused assertion per test where practical
- Test both success and failure scenarios
- Cover null and invalid input conditions for public methods
- Prefer testing behavior through public APIs instead of implementation details
- Avoid introducing test-only production hooks

Use these conventions for test names:
- WhenXThenY
- Example: WhenOrderIsMissingThenServiceThrows

Example:
```csharp
[Test]
public void WhenOrderIdIsNegativeThenThrowArgumentOutOfRangeException()
{
    Assert.Throws<ArgumentOutOfRangeException>(() => _service.GetById(-1));
}
```

## 12. Documentation and Change Management

When adding a feature:
- Update the architecture notes if the structure or layering changes
- Document new public APIs and important behavior
- Keep the code and documentation consistent with each other

## 13. Agent and Developer Workflow for New Features

When implementing a new feature, follow this sequence:
1. Identify the owning layer and project
2. Define the domain model or contract first
3. Implement the business logic or workflow
4. Expose the behavior through the appropriate service or endpoint
5. Add tests for the main success and failure paths
6. Update documentation if the feature changes the architecture or public behavior

## 14. Review Checklist

Before completing a feature, verify that:
- The code is placed in the correct project
- Public APIs are documented
- Dependencies are injected through constructors where appropriate
- Async methods use cancellation tokens and avoid blocking calls
- Tests cover the critical behavior
- The change does not violate the intended layer boundaries
