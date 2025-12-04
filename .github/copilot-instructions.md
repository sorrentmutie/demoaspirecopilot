# Demo ECommerce - GitHub Copilot Instructions

## Project Overview
Web application for managing an ecommerce
Built with .NET 10+, MVC,  Minimal API, and SQL Server.

---

## Tech Stack & Architecture

### Frontend
- **MVC** (.NET 10+)
- Responsive design, mobile-first
- SignalR for real-time updates (built-in with MVC)

### Backend
- **.NET 10+ Minimal API**
- Clean Architecture principles
- Repository pattern for data access
- MediatR for CQRS (optional)
- FluentValidation for input validation
- Serilog for structured logging

### Database
- **SQL Server 2019+**
- Entity Framework Core 10+ (Code First)
- Migrations for schema changes
- See: `database-schema.sql` for complete schema

### Cloud & Storage
- Azure Blob Storage for cover images
- Azure App Service for hosting
- Azure SQL Database (or SQL Server on-premise)

### External APIs
- ComicVine API (https://comicvine.gamespot.com/api/)
- Marvel API (https://developer.marvel.com/)
- Rate limiting and caching required

---

## Domain Model

### Core Entities
```



```
### Key Concepts

### Key Business Rules

---

## Coding Standards

### C# / .NET
- **Target Framework**: .NET 10.0
- **Nullable Reference Types**: Enabled
- **File-scoped namespaces**: Always use
- **Minimal API style**: Prefer over Controller-based
- **Async/await**: All I/O operations must be async
- **Records**: Use for DTOs and value objects
- **LINQ**: Prefer method syntax over query syntax

### Naming Conventions
- Controllers/Endpoints: Plural nouns (e.g., `/api/products`, not `/api/product`)
- Entities: Singular Pascal Case (e.g., `Product`, `ProductEdition`)
- DTOs: Suffix with `Dto` (e.g., `ProductDto`, `CreateProductRequest`)
- Interfaces: Prefix with `I` (e.g., `IProductRepository`)
- Private fields: `_camelCase`

### API Endpoints Structure
```
GET    /api/comics                    # List all comics
GET    /api/comics/{id}               # Get single comic
POST   /api/comics                    # Create comic
PUT    /api/comics/{id}               # Update comic
DELETE /api/comics/{id}               # Delete comic
GET    /api/comics/search?q={query}   # Search comics
GET    /api/comics/{id}/editions      # Get comic editions
POST   /api/comics/{id}/cover         # Upload cover image
```

### Response Format
Always return consistent JSON responses:
```json
{
  "success": true,
  "data": { ... },
  "errors": [],
  "meta": {
    "timestamp": "2025-11-06T10:00:00Z",
    "pagination": { "page": 1, "pageSize": 20, "total": 100 }
  }
}
```

### Error Handling
- Use global exception middleware
- Return ProblemDetails for errors (RFC 7807)
- Log all exceptions with Serilog
- Never expose stack traces in production

---

## Project Structure

```

```

---

## Important Patterns & Practices

### Repository Pattern
```csharp
public interface IComicRepository
{
    Task<Comic?> GetByIdAsync(int id, CancellationToken ct = default);
    Task<IEnumerable<Comic>> GetAllAsync(CancellationToken ct = default);
    Task<int> AddAsync(Comic comic, CancellationToken ct = default);
    Task UpdateAsync(Comic comic, CancellationToken ct = default);
    Task DeleteAsync(int id, CancellationToken ct = default);
    Task<IEnumerable<Comic>> SearchAsync(string query, CancellationToken ct = default);
}
```

### DTOs vs Entities
- NEVER return entities directly from API
- Always map Entity → DTO using AutoMapper or Mapster
- DTOs are flat, entities can be complex

### Validation
- Use FluentValidation for all input DTOs
- Validate at API boundary, not in domain
- Return 400 Bad Request with validation errors

### Logging
- Log all API requests (duration, status code)
- Log all database queries > 1s
- Use structured logging with Serilog
```csharp
logger.LogInformation("Comic {ComicId} created by {User}", comicId, userId);
```

---

## External API Integration

## Security Considerations

### Secrets Management
- NEVER commit API keys, connection strings
- Use **User Secrets** for local development
- Use **Azure Key Vault** for production
- appsettings.json contains only non-sensitive defaults

### Input Validation
- Sanitize all user input
- Validate file uploads (size, type)
- Use parameterized queries (EF Core does this automatically)

### CORS
- Allow only specific origins in production
- Be explicit about allowed methods and headers

---

## Testing Guidelines

### Unit Tests
- Test business logic in isolation
- Mock external dependencies (repos, APIs)
- Use xUnit, FluentAssertions, Moq

### Integration Tests
- Test API endpoints end-to-end
- Use in-memory database or TestContainers
- WebApplicationFactory for API testing

### Code Coverage
- Target: >80% coverage
- Use `dotnet test --collect:"XPlat Code Coverage"`
- SonarQube for quality gates

---

## Common Pitfalls to Avoid

### ❌ DON'T
- Return `IQueryable` from repositories (breaks abstraction)
- Use `DateTime` (use `DateTime2` or `DateTimeOffset`)
- Catch exceptions without logging
- Use `string` for enums (use actual enums)
- Mix business logic in controllers/endpoints
- Forget to dispose DbContext (use DI with scoped lifetime)

### ✅ DO
- Use `CancellationToken` for all async methods
- Implement pagination for list endpoints
- Use DTOs for API contracts
- Log performance metrics
- Write integration tests for critical paths
- Use `ValueTask` for potentially sync operations

---

### API Calls
- Use `HttpClient` injected via DI for external APIs
- For internal data, use services directly (no HTTP needed in Blazor Server)
- Always show loading state during operations
- Handle errors gracefully (don't crash UI)

### Performance
- Use `@key` for list rendering
- Lazy load components with `@defer`
- Virtualize long lists with `Virtualize` component
- Dispose event handlers and subscriptions properly
- Use `ShouldRender()` to optimize re-renders

---

## Questions to Ask Before Generating Code

When implementing a feature, Copilot should consider:
1. Which layer does this belong to? (API, Application, Domain, Infrastructure)
2. Does this need a DTO?
3. Should this be async?
4. What validation is needed?
5. What error cases exist?
6. Should this be cached?
7. What should be logged?
8. Is pagination needed?

---

## References
- Product Requirements: `comic-collection-prd.md`
- Database Schema: `database-schema.sql`
- .NET Minimal API Docs: https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis
- Blazor Docs: https://learn.microsoft.com/en-us/aspnet/core/blazor
- EF Core Docs: https://learn.microsoft.com/en-us/ef/core/

---

## Preferred Libraries
- **Validation**: FluentValidation
- **Mapping**: Mapster (or AutoMapper)
- **Logging**: Serilog
- **Testing**: xUnit, FluentAssertions, Moq
- **HTTP Client**: Refit (for typed API clients)
- **UI Components**: Microsoft.FluentUI.AspNetCore.Components
- **Image Processing**: SixLabors.ImageSharp

---

## When in Doubt...
- Follow official Microsoft guidelines
- Prioritize readability over cleverness
- Add XML documentation for public APIs
- Ask clarifying questions before implementation

