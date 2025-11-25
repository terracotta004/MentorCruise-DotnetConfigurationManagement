# MentorCruise-DotnetConfigurationManagement
## 🧭 My local setup

- **OS:** Windows
- **Shell:** PowerShell (v5.1)
- **Target .NET:** net9.0 (project builds/runs targeting .NET 9)

How to run locally (PowerShell):

```powershell
cd MentorCruise-DotnetConfigurationManagement
dotnet run
```

To run with a specific environment (e.g. Development) or set environment variables, use this Powershell statement to set a session-level environment variable:

```powershell
$env:ASPNETCORE_ENVIRONMENT='Development'; dotnet run
```

Alternatively, search for "Edit the system environment variables" in the Windows Start Menu to set a persistent user or system level environment variable. [See this guide for help with persistent environment variables.](https://www.howtogeek.com/787217/how-to-edit-environment-variables-on-windows-10-or-11/)

This repo includes `appsettings.json` and `appsettings.Development.json` for environment-specific configuration.

### About `Program.cs`

`Program.cs` is a small but useful example showing how a .NET app builds and reads configuration:

- It creates a `ConfigurationBuilder` and loads:
  - `appsettings.json` (required)
  - `appsettings.{ASPNETCORE_ENVIRONMENT}.json` (optional — chosen by the `ASPNETCORE_ENVIRONMENT` env var, defaulting to `Production`)
  - Environment variables (so they can override JSON settings)

- The builder produces an `IConfiguration` instance which the program uses to:
  - Read values (e.g. `Logging:LogLevel:Default`, `AllowedHosts`)
  - Bind whole sections to strongly-typed classes (`LoggingConfiguration` and `AppSettings`) for easy, safe access

- The project demonstrates environment overrides and environment variables in action — e.g. checking `IS_ENVIRONMENT_VARIABLE_GURU`, setting `APP_NAME` at runtime and using that value to replace `AppSettings.ApplicationName`.

See `Program.cs` and `AppSettings.cs` for the concrete examples used in this repository.





# Configuration Management in .NET

## Overview

Configuration management is a critical aspect of building scalable and maintainable .NET applications. This guide covers:

- Using `appsettings.json` for environment-specific settings
- Leveraging environment variables
- Supporting multiple environments via `appsettings.{Environment}.json`
- Accessing configuration using `IConfiguration`
- Binding configuration to strongly typed classes, including nested classes and arrays

---

## 📘 Resources

Here are some official and community resources to deepen your understanding:

- [Configuration in ASP.NET Core (Microsoft Docs)](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-9.0) [[learn.microsoft.com]](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-9.0)
- [Options Pattern in ASP.NET Core (Microsoft Docs)](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-9.0) [[learn.microsoft.com]](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-9.0)
- [Environment Variables in .NET](https://www.compilenrun.com/docs/framework/dotnet/net-deployment/net-environment-variables/) [[compilenrun.com]](https://www.compilenrun.com/docs/framework/dotnet/net-deployment/net-environment-variables/)
- [Using Multiple appsettings.json Files](https://stackoverflow.com/questions/46364293/automatically-set-appsettings-json-for-dev-and-release-environments-in-asp-net-c) [[stackoverflow.com]](https://stackoverflow.com/questions/46364293/automatically-set-appsettings-json-for-dev-and-release-environments-in-asp-net-c)

---

## 🛠 Configuration Basics

### 1. `appsettings.json` and Environment-Specific Files

Use `appsettings.json` for default settings and override them with environment-specific files like:

Plain Text

appsettings.Development.json

appsettings.Production.json

Show more lines

These are automatically loaded based on the `ASPNETCORE_ENVIRONMENT` variable.

### 2. Environment Variables

Environment variables can override settings in `appsettings.json`. They are useful for secrets and deployment-specific values.

```jsx
export ConnectionStrings__DefaultConnection="Server=prod;Database=live;"
```

In .NET, use:

```jsx
builder.Configuration.AddEnvironmentVariables();
```

### 3. Accessing Configuration via `IConfiguration`

```jsx
public class MyService
{
    private readonly IConfiguration _config;

    public MyService(IConfiguration config)
    {
        _config = config;
    }

    public void PrintSetting()
    {
        var value = _config["AppSettings:Setting1"];
        Console.WriteLine(value);
    }
}

```

---

## 🔒 Strongly Typed Configuration

### Example: Nested Classes and Arrays

**appsettings.json**

```jsx

{
  "AppSettings": {
    "ApplicationName": "MyApp",
    "Features": {
      "EnableLogging": true,
      "Modules": ["Auth", "Payments", "Reports"]
    }
  }
}

```

**Configuration Classes**

```jsx
public class AppSettings
{
    public string ApplicationName { get; set; }
    public Features Features { get; set; }
}

public class Features
{
    public bool EnableLogging { get; set; }
    public string[] Modules { get; set; }
}
```

**Binding in Program.cs**

C#

```jsx
builder.Services.Configure<AppSettings>(
    builder.Configuration.GetSection("AppSettings"));
```

---

## 🧪 Assignment

Create a .NET Console or Web API project that:

1. Uses `appsettings.json` and `appsettings.Development.json`
2. Reads configuration using `IConfiguration`
3. Binds settings to a strongly typed class with nested properties and arrays
4. Overrides one setting using an environment variable
5. Outputs the configuration values to the console or an endpoint

### ✅ Bonus

- Use `IOptions<AppSettings>` for dependency injection
- Add a README.md explaining your setup