# Serilog.Sinks.Sentry

[![Build status](https://ci.appveyor.com/api/projects/status/3rtn2dsk5ln6qaup?svg=true)](https://ci.appveyor.com/project/olsh/serilog-sinks-sentry)
[![Quality Gate](https://sonarqube.com/api/badges/gate?key=serilog-sinks-sentry)](https://sonarqube.com/dashboard/index/serilog-sinks-sentry)
[![NuGet](https://img.shields.io/nuget/v/Serilog.Sinks.Sentry.svg)](https://www.nuget.org/packages/Serilog.Sinks.Sentry/)

A Sentry sink for Serilog.

## Installation

The library is available as a [Nuget package](https://www.nuget.org/packages/Serilog.Sinks.Sentry/).
```
Install-Package Serilog.Sinks.Sentry
```

## Get started

### Adding Sentry sink

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Sentry("Sentry DSN")
    .Enrich.FromLogContext()
    .CreateLogger();

// By default, only messages with level errors and higher are captured
log.Error("This error goes to Sentry.");
```

### Capturing HttpContext (ASP.NET Core)

In order to capture user, request body and headers, some additional steps are required.

Specify custom HttpContext destructing policy
```csharp
var log = new LoggerConfiguration()
    .WriteTo.Sentry("Sentry DSN")
    .Enrich.FromLogContext()

    // Add this two lines to the logger configuration
    .Destructure.With<HttpContextDestructingPolicy>()
    .Filter.ByExcluding(e => e.Exception?.IsCaptured() == true)

    .CreateLogger();
```

Add Sentry context middleware in Startup.cs
````csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery, IHostingEnvironment env, ILoggerFactory loggerFactory, IApplicationLifetime appLifetime)
{
    // Add this line
    app.AddSentryContext();

    // Other stuff
}
````

## Known issues
At the moment only .NET Framework and .NET Core 2.0 supported.
