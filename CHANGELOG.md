# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- Rule requiring CHANGELOG.md update on version bumps

## [1.2.0] - 2026-01-09

### Added
- `allowed-tools` and `argument-hint` to work-items commands
- `argument-hint` to adr and dp-tools commands
- Cross-references between related work-items commands
- System requirements documentation in ADR README

### Changed
- Extract ADR hooks into shell scripts (`adr-lint.sh`, `adr-index.sh`)
- Improve azure-boards skill `when_to_use` triggers with specific scenarios
- Update CLAUDE.md with hook patterns and frontmatter syntax

### Removed
- AGENTS.md (consolidated into CLAUDE.md)

## [1.1.0] - 2026-01-08

### Added
- Support for Claude Code 2.1.0 features
- `user-invocable: false` to skills (hide from `/` menu)
- `once: true` to ADR index regeneration hook
- CHANGELOG.md for version tracking

### Changed
- `allowed-tools` format from comma-separated to YAML list style

## [1.0.0] - Initial Release

### Added
- work-items plugin: analyze, optimize, tasks commands
- dp-tools plugin: create-claude-md, create-rules commands
- azure-boards plugin: Azure DevOps integration skill
- adr plugin: ADR creation, auto-linting, index regeneration
