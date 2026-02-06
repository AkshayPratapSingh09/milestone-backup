# Milestone Notes & Snippets

```csharp
//For InMemeory 
builder.Services.AddDbContext<ProductApi.Data.ProductWebApiContext>(options =>
    options.UseInMemoryDatabase("ProductDb"));

// NEW (Correct)
options.UseInMemoryDatabase("ProductDb");

// For Sqlite
builder.Services.AddDbContext<ProductApi.Data.ProductWebApiContext>(options =>
    options.UseSqlServer(...)); // or UseSqlite(...)

```

##  When using InMemroy do not use below two commands
```powershell
Add-Migration InitialCreate
```
```powershell
Update-Database
```

```json
"ConnectionStrings": {
  "AppDbContext": "Data Source=product.db"
}
```

```json
{
  "name": "Laptop",
  "price": 999.99,
  "description": "Gaming Laptop"
}
```

```csharp
public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; } = string.Empty;
        public decimal Price { get; set; }
        public string? Description { get; set; }
    }
```

```json
  {
  "id": 1,
  "name": "Laptop Pro",
  "price": 1200.00,
  "description": "Updated Laptop"
}
```

```text
C:\Users\admin\source\repos
```

```csharp
// MiddleWare with Ilogger Integrated Code 

using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net;
using System.Text.Json;
using System.Threading.Tasks;

namespace ProductApi.Middlewares
{
    public class ExceptionMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<ExceptionMiddleware> _logger;

        public ExceptionMiddleware(RequestDelegate next, ILogger<ExceptionMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task Invoke(HttpContext httpContext)
        {
            try
            {
                await _next(httpContext);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Something went wrong: {Message}", ex.Message);
                await HandleExceptionAsync(httpContext, ex);
            }
        }

        private static Task HandleExceptionAsync(HttpContext context, Exception exception)
        {
            context.Response.ContentType = "application/json";
            context.Response.StatusCode = (int)HttpStatusCode.InternalServerError;

            var response = new
            {
                StatusCode = context.Response.StatusCode,
                Message = "Internal Server Error from Middleware",
                Detailed = exception.Message
            };

            var json = JsonSerializer.Serialize(response);

            return context.Response.WriteAsync(json);
        }
    }

    public static class MiddlewareExtensions
    {
        public static IApplicationBuilder UseExceptionMiddleware(this IApplicationBuilder builder)
        {
            return builder.UseMiddleware<ExceptionMiddleware>();
        }
    }
}
```

## Add Middleware in Program.cs
```csharp
app.UseMiddleware<ProductApi.Middleware.ExceptionMiddleware>();
```
