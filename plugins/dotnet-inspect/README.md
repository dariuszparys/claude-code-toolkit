# dotnet-inspect

Inspect .NET assemblies, explore NuGet package APIs, search types, compare versions, and check vulnerabilities.

## Skills

### dotnet-inspect

Auto-invoked skill for .NET API exploration tasks. Triggers on:
- "What methods does X have"
- "Find types like Y"
- "Compare versions"
- "Check for vulnerabilities"
- "Explore this package"

## Requirements

- .NET 10+ SDK

## Usage

The skill uses `dnx` (like npx for .NET) to run the tool without global installation:

```bash
dnx dotnet-inspect -y -- <command>
```

See `skills/SKILL.md` for full command reference.
