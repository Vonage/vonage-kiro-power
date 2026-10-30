# Changelog

All notable changes to this project will be documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Added

- Initial release of Vonage CPaaS Power for Kiro
- `vonage-api` MCP server with tools for account management, phone numbers, applications, and reports
- `vonage-docs` MCP server for developer documentation, SDK references, and code examples
- Get Started workflow: credential validation, application creation, number search/purchase, and environment variable output
- Reports & Monitoring workflow: activity records, delivery rates, channel distribution, and balance checks
- Steering files for guided AI workflows
- `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`, and `LICENSE` (Apache 2.0)
- `plugin.json` Agent Plugin manifest
- `get-started` and `reports-monitoring` skills under `skills/`, replacing the steering files
- Shared reference files in `skills/get-started/references/` (credential gate, environment variables, plugin overview), replacing the `POWER.md` body
- "Migrating from the Kiro power" section in `README.md`

### Changed

- Adopted the [Agent Plugins 1.0.0](https://github.com/agentplugins/agent-plugins-spec/blob/main/spec/1.0.0.md) open standard with client-neutral wording; Kiro is one example client
- Renamed the plugin from `vonage-kiro-power` to `vonage-cpaas`
- `mcp.json` now declares `$schema` and a `type` for each server (`stdio` for `vonage-api`, `streamable-http` for `vonage-docs`)
- The `vonage-api` server now loads Vonage credentials from the `.env` file in the plugin data directory (`${PLUGIN_DATA}/.env`), set through `DOTENV_CONFIG_PATH` in `mcp.json`

### Removed

- The `${VAR}` credential placeholders from the `vonage-api` `env` block in `mcp.json`
- `POWER.md` and the `steering/` directory
