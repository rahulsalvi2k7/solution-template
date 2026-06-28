# Solution Template

This repository is a starter solution for building a layered ASP.NET Core application on .NET 8. It provides a simple structure for separating web hosting, business logic, domain models, and data access concerns while keeping the project easy to extend.

## What this solution is for

Use this template as a foundation for new services or APIs that need:
- a clear application entry point
- business logic separated from HTTP concerns
- shared domain models and contracts
- test projects organized by scope

## Project structure

- [src/Business.Service](src/Business.Service) - ASP.NET Core host and API entry point.
- [src/Business.Specifications](src/Business.Specifications) - Business workflows, use cases, and orchestration logic.
- [src/Business.Models](src/Business.Models) - Shared domain models, DTOs, and contracts.
- [src/Business.Data](src/Business.Data) - Data access and persistence scaffolding.
- [tests](tests) - Unit, integration, benchmark, and assertion test projects.
- [docs](docs) - Architecture and development guidance.

## Documentation

- [docs/architecture.md](docs/architecture.md) - Overview of the solution architecture and component relationships.
- [docs/coding-standards.md](docs/coding-standards.md) - Coding conventions for developers and agents working on the solution.

## Development notes

- Target framework: .NET 8
- Web host: ASP.NET Core
- Test framework: NUnit
- Architecture style: layered / clean-architecture inspired structure

This template is intentionally lightweight at the start, but it is structured so new features can be added in a consistent way as the solution grows.

## Use this solution template from NuGet

To install the packaged template locally from the generated NuGet package:

```bash
dotnet new install ./bin/Release/Business.Microservice.Template.1.0.1.nupkg
```

To create a new solution from the installed template:

```bash
dotnet new business-microservice -n My.New.Service
```

To update the local installation later, re-pack and reinstall the package:

```bash
dotnet pack SolutionTemplate.Template.csproj -c Release
dotnet new install ./bin/Release/Business.Microservice.Template.1.0.1.nupkg
```

If you need to remove an old local template install, use:

```bash
dotnet new uninstall Business.Microservice.Template
```

## Template customization tips

- Rename the solution and project names after scaffolding to match the business domain.
- Keep the layered structure intact for new features.
- Add domain-specific endpoints, services, entities, and tests in the respective projects.
