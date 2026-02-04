---
name: dotnet-inspect
description: This skill should be used when inspecting .NET assemblies, exploring NuGet package APIs, searching for types across frameworks, comparing API versions, or checking package vulnerabilities. Triggers on "what methods does X have", "find types like Y", "compare versions", "check for vulnerabilities", "explore this package", or any .NET API exploration task.
user-invocable: false
---

# dotnet-inspect

CLI tool for inspecting .NET assemblies and NuGet packages.

## Requirements

- .NET 10+ SDK

## Installation

Use `dnx` to run without global installation (like npx for Node):

```bash
dnx dotnet-inspect -y -- <command>
```

**Important:**
- Always use `-y` to skip the interactive confirmation prompt (which breaks LLM tool use). New package versions also trigger this prompt.
- Always use `--` to separate dnx options from tool arguments. Without it, `--help` shows dnx help, not dotnet-inspect help.

## Quick Reference

```bash
dotnet-inspect <package>[@version]           # Package metadata
dotnet-inspect api <type> --package <pkg>    # API from package
dotnet-inspect api --platform <assembly>     # API from installed SDK
dotnet-inspect platform                      # List installed frameworks
dotnet-inspect find <pattern>                # Search types in runtime
dotnet-inspect assembly path/to/file.dll     # Local assembly
dotnet-inspect diff <type> --package         # Version comparison
dotnet-inspect type <type> --package <pkg>   # Type shape with hierarchy
```

## Command Selection Guide

| Goal | Command |
|------|---------|
| Find a type by name | `dotnet-inspect find "*Pattern*"` |
| See what's in a package | `dotnet-inspect api --package <pkg>` |
| Get type details with members | `dotnet-inspect type <type> --package <pkg>` |
| Check specific method overloads | `dotnet-inspect api <type> --package <pkg> -m <method> -v:d` |
| Compare a type between versions | `dotnet-inspect diff <type> --package <pkg>@v1..v2` |
| Compare entire packages | Use `api -v:q` on both versions, compare counts |
| Check for vulnerabilities | `dotnet-inspect <package>@<version>` |
| See interfaces a type implements | `dotnet-inspect api <type> --package <pkg> --interfaces` |

## Common Workflows

### Discovery Workflow

When the location of a type is unknown:

```bash
# Step 1: Find the type
dotnet-inspect find "JsonSerializer" --framework runtime
# Shows: System.Text.Json.JsonSerializer in System.Text.Json.dll

# Step 2: Get overview with type command
dotnet-inspect type JsonSerializer --platform System.Text.Json

# Step 3: Drill into specific members
dotnet-inspect api JsonSerializer --platform System.Text.Json -m Serialize -v:d
```

### Package Analysis Workflow

```bash
# Check package metadata and vulnerabilities
dotnet-inspect System.Text.Json@8.0.4

# List available versions
dotnet-inspect System.Text.Json --versions -n 10

# Audit assemblies for SourceLink/determinism
dotnet-inspect assembly --package System.Text.Json --tfm net8.0 --audit
```

### API Comparison Workflow

The `diff` command compares a **specific type** between versions. It requires a valid type name.

```bash
# Step 1: Find valid type names in the package first
dotnet-inspect find "*" --package FastEndpoints@7.1.0 -n 20

# Step 2: Diff a specific type between versions
dotnet-inspect diff Config --package FastEndpoints@7.1.0..7.2.0
dotnet-inspect diff JsonSerializer --package System.Text.Json@9.0.0..10.0.2
```

**Important:** The `diff` command fails if the type doesn't exist in either version. Always use `find` first to discover valid type names.

### Full Package Comparison Workflow

To compare **entire packages** between versions (not just one type):

```bash
# Step 1: List all types in each version (run in parallel for efficiency)
dotnet-inspect api --package FastEndpoints@7.1.0 -v:q
dotnet-inspect api --package FastEndpoints@7.2.0 -v:q
```

The `-v:q` output shows summary counts at the top:
```
**Types:** 144
**Methods:** 603
**Properties:** 277
```

Compare these counts to quickly see the scope of changes.

```bash
# Step 2: Diff key types that exist in both versions (run in parallel)
dotnet-inspect diff Config --package FastEndpoints@7.1.0..7.2.0
dotnet-inspect diff IEndpoint --package FastEndpoints@7.1.0..7.2.0
dotnet-inspect diff HttpClientExtensions --package FastEndpoints@7.1.0..7.2.0
```

**Identifying added/removed types:** Compare the type lists from both `-v:q` outputs. Types present in v1 but not v2 were removed; types in v2 but not v1 were added.

**Diff output format:**
```
## API Diff: Config
**7.1.0** → **7.2.0**
**Summary:** +0 added, -2 removed

### Removed
- `IServiceResolver ServiceResolver { get; set; }`

### Added
+ `string NewProperty { get; set; }`
```

## Commands

### package

Get package metadata, check vulnerabilities, list versions.

```bash
dotnet-inspect System.Text.Json                    # Latest version
dotnet-inspect System.Text.Json@8.0.4 -v:d         # Specific version, detailed
dotnet-inspect System.Text.Json --versions -n 5    # List versions
```

### api

Explore types and members in packages or platform assemblies.

```bash
dotnet-inspect api --package System.Text.Json                     # List all types
dotnet-inspect api --package System.Text.Json --filter "*Json*"   # Filter by glob
dotnet-inspect api JsonSerializer --package System.Text.Json      # Single type
dotnet-inspect api JsonSerializer --package System.Text.Json@9.0.0  # Specific version
dotnet-inspect api JsonSerializer --package System.Text.Json -m Serialize  # Single member
dotnet-inspect api JsonSerializer --package System.Text.Json --signatures-only  # Plain output
dotnet-inspect api 'Option<T>' --package System.CommandLine       # Generic types
dotnet-inspect api Command --package System.CommandLine --ctor    # Constructors
dotnet-inspect api JsonSerializer --package Newtonsoft.Json --docs  # With documentation
dotnet-inspect api --platform System.Text.Json                    # Platform assembly
dotnet-inspect api JsonSerializer --platform System.Text.Json --interfaces  # With interfaces
```

### type

Show type shape with hierarchy and members in tree format.

```bash
dotnet-inspect type JsonSerializer --package System.Text.Json
dotnet-inspect type "List<T>" --platform System.Collections
dotnet-inspect type JsonSerializer --package System.Text.Json --json
```

### find

Search for types across packages, assemblies, and frameworks.

```bash
dotnet-inspect find HttpClient                          # Search runtime (default)
dotnet-inspect find "*Stream*" -n 10                    # Glob pattern, limit results
dotnet-inspect find "*Json*" --package System.Text.Json # Search in package
dotnet-inspect find "*Serializer*" --package System.Text.Json --package Newtonsoft.Json
dotnet-inspect find "*Command*" --project ./MyApp.csproj  # Project dependencies
dotnet-inspect find "*Service*" --bin ./bin/Debug/net8.0  # Built output directory
dotnet-inspect find ILogger --framework aspnetcore      # ASP.NET Core framework
```

| Scope | Description |
|-------|-------------|
| (none) | Defaults to `--framework runtime` |
| `--package` | NuGet package (name or name@version) |
| `--assembly` | Local assembly file |
| `--platform` | Specific platform assembly |
| `--framework` | All assemblies in framework (runtime, aspnetcore) |
| `--project` | Project dependencies via project.assets.json |
| `--bin` | All DLLs in output directory |

### diff

Compare a **specific type's** API surface between versions. Requires a valid type name that exists in both versions.

```bash
# Package version comparison
dotnet-inspect diff JsonSerializer --package System.Text.Json@9.0.0..10.0.2
dotnet-inspect diff Config --package FastEndpoints@7.1.0..7.2.0

# Platform version comparison
dotnet-inspect diff JsonSerializer --platform System.Text.Json@8.0.23..10.0.2
dotnet-inspect diff HttpClient --platform System.Net.Http@9.0.0..10.0.0 --framework runtime
```

**Important:**
- The type name must exist in **both** versions, or the command fails
- Use `find` first to discover valid type names: `dotnet-inspect find "*" --package pkg@version`
- This command compares one type at a time, not entire packages
- Some types may cause internal errors (e.g., "Argument_AddingDuplicateWithKey") — skip these and try other types

### platform

List installed frameworks and inspect platform assemblies.

```bash
dotnet-inspect platform                            # List installed frameworks
dotnet-inspect platform --list-versions            # Installed SDK versions
dotnet-inspect platform --framework runtime        # List runtime assemblies
dotnet-inspect platform --framework aspnetcore     # List ASP.NET Core assemblies
```

## Package vs Platform

Some assemblies exist in both NuGet packages and the installed SDK:

| Source | When to Use |
|--------|-------------|
| `--package` | Third-party libraries, specific versions, packages not in SDK |
| `--platform` | .NET runtime/SDK assemblies, comparing SDK versions, no network needed |

```bash
# From NuGet package (downloads if needed)
dotnet-inspect api JsonSerializer --package System.Text.Json@9.0.0

# From installed SDK (no download)
dotnet-inspect api JsonSerializer --platform System.Text.Json
```

## Output Options

| Verbosity | Output |
|-----------|--------|
| `-v:q` | Title + compact line |
| `-v:m` | + description (default) |
| `-v:n` | + Metadata table |
| `-v:d` | + Full member signatures, Statistics, Dependencies |

| Format | Description |
|--------|-------------|
| `--json` | Full JSON output |
| `--json --compact` | Minified JSON, nulls omitted |
| `--signatures-only` | Plain method signatures (api command) |

Section filtering (for verbose output):
```bash
dotnet-inspect package --discover                  # List sections with numbers
dotnet-inspect System.Text.Json -v:d -x:3,4        # Exclude sections 3 and 4
dotnet-inspect System.Text.Json -v:d -s:1,2        # Include only sections 1 and 2
```

## Key Options

| Option | Description |
|--------|-------------|
| `--tfm net8.0` | Select target framework |
| `-m Name` | Filter to member |
| `--filter "Pattern*"` | Glob filter for types |
| `--ctor` | Constructor details |
| `--docs` | Fetch XML documentation |
| `--all` | Include hidden/obsolete |
| `--unsafe` | Filter to pointer methods |
| `--audit` | SourceLink/determinism check |
| `--deps` | Show runtime dependencies |
| `--interfaces` | Show implemented interfaces |

## Generic Type Syntax

Use C# generic syntax - the tool automatically converts to metadata format:
- `"List<T>"` → `List\`1`
- `"Dictionary<TKey, TValue>"` → `Dictionary\`2`

Quote the type name to prevent shell interpretation.

## Common Assembly Names

| Type | Platform Assembly |
|------|-------------------|
| List, Dictionary, Queue | System.Collections |
| String, Int32, Object, Span | System.Runtime |
| JsonSerializer, JsonDocument | System.Text.Json |
| HttpClient | System.Net.Http |
| Regex, Match | System.Text.RegularExpressions |
| Task, Async | System.Threading.Tasks |
| File, Stream, Path | System.IO |

## Tips

- Glob patterns must be quoted: `"*Stream*"` (prevents shell expansion)
- Member filter (`-m`) is case-insensitive
- Uses NuGet cache; `--verbose` shows cache activity
- For full overload details, use `-v:d` with `-m` filter
- Run independent commands in parallel for efficiency (e.g., `api -v:q` for both versions, multiple `diff` commands)
- When comparing packages, look at Types/Methods/Properties counts first to gauge scope of changes
