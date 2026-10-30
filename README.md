# Vonage CPaaS Agent Plugin

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Agent Plugins](https://img.shields.io/badge/Agent%20Plugins-1.0.0-blueviolet)](https://github.com/agentplugins/agent-plugins-spec/blob/main/spec/1.0.0.md)
[![Vonage](https://img.shields.io/badge/Vonage-CPaaS-brightgreen)](https://developer.vonage.com)

An [Agent Plugin](https://github.com/agentplugins/agent-plugins-spec/blob/main/spec/1.0.0.md) that brings the Vonage Communications Platform directly into your AI-assisted development workflow. It is packaged in the Agent Plugins 1.0.0 open format, so it loads in any compatible agent client, such as [Kiro](https://kiro.dev). Go from zero to a working Vonage integration in minutes with guided, conversational workflows.

---

## Table of Contents

- [What Is This?](#what-is-this)
- [Plugin Layout](#plugin-layout)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Migrating from the Kiro power](#migrating-from-the-kiro-power)
- [Workflows](#workflows)
- [Available Tools](#available-tools)
- [Documentation](#documentation)
- [Contributing](#contributing)
- [Code of Conduct](#code-of-conduct)
- [License](#license)

---

## What Is This?

This plugin connects your agent client to the Vonage CPaaS platform via two MCP servers:

| Server | Description |
|---|---|
| **`vonage-api`** | Live API bindings for account management, phone numbers, applications, and activity reports |
| **`vonage-docs`** | Vonage developer documentation, SDK references, and code examples |

Combined with skills that encode best-practice workflows, your agent can guide you through tasks like setting up a new Vonage application, purchasing a phone number, and pulling activity reports — all through natural conversation.

---

## Plugin Layout

The plugin follows the Agent Plugins 1.0.0 layout:

```text
.
├── plugin.json            # plugin manifest
├── mcp.json               # MCP servers: vonage-api and vonage-docs
└── skills/
    ├── get-started/
    │   ├── SKILL.md
    │   └── references/    # shared by both skills
    │       ├── credential-gate.md
    │       ├── environment-variables.md
    │       └── overview.md
    └── reports-monitoring/
        └── SKILL.md
```

---

## Prerequisites

- An agent client that supports [Agent Plugins 1.0.0](https://github.com/agentplugins/agent-plugins-spec/blob/main/spec/1.0.0.md), such as [Kiro](https://kiro.dev)
- A [Vonage API account](https://ui.idp.vonage.com/ui/auth/registration) (free to create)
- Your Vonage API Key and Secret from the [Vonage Dashboard](https://dashboard.nexmo.com/settings)

---

## Installation

1. Clone this repository:

   ```bash
   git clone https://github.com/Vonage/vonage-kiro-power.git
   ```

2. Install the plugin in your agent client from the cloned folder. Your client discovers the skills in `skills/` and the MCP servers in `mcp.json`. For example, in Kiro, open the Powers panel, add a custom power, and choose **Import power from a folder** (pick the cloned folder) or **Import power from GitHub** (paste the repository URL). See [Install powers](https://kiro.dev/docs/powers/installation/) for the current steps.

3. Add your Vonage credentials to the plugin's `.env` file, which sits directly inside the plugin data directory:

   ```text
   VONAGE_API_KEY=your_api_key_here
   VONAGE_API_SECRET=your_api_secret_here
   ```

   Find both values at [Vonage Dashboard → API Settings](https://dashboard.nexmo.com/settings). See [Configuration](#configuration) for where the file is in your client and for the full list of variables.

4. Restart your agent client so the `vonage-api` server starts with the new values.

> **Credentials not set?** When you trigger a workflow, the skills detect missing credentials and walk you through the setup before proceeding.

---

## Configuration

Set the following environment variables in the plugin's `.env` file, one `NAME=value` per line. The `vonage-api` server loads that file when your agent client starts the server, so restart the client after every change.

The file is `.env` directly inside the plugin data directory: a folder your agent client creates for this plugin, keeps across plugin updates, and passes to the server as `PLUGIN_DATA` (see [Agent Plugins §9.1](https://github.com/agentplugins/agent-plugins-spec/blob/main/spec/1.0.0.md#91-subprocess-environment)). `mcp.json` points the server at `${PLUGIN_DATA}/.env` and holds no credentials. Check your client's documentation for where it keeps plugin data. For example, Kiro uses:

```text
~/.kiro/powers/data/vonage-kiro-power/.env
```

Kiro names that folder after the installed power, which is the name of the folder you installed from, or the repository name for a GitHub install. If your clone is in a folder with a different name, use that name. On Windows, the Kiro path is `%USERPROFILE%\.kiro\powers\data\vonage-kiro-power\.env`. On macOS and Linux, restrict the file to your user with `chmod 600`.

> **Project `.env` files are not loaded.** The `vonage-api` server reads only the plugin's `.env` file. It does not load a `.env` file in your project root.
>
> **Already-set variables win.** If a Vonage variable is already set in the environment your agent client starts the `vonage-api` server with, the server keeps that value and ignores the same variable in the plugin's `.env` file.
>
> **Credential check still failing?** Some agent clients do not expand `${PLUGIN_DATA}` yet. See [If the recovery message still appears](#if-the-recovery-message-still-appears).

### Required for all workflows

| Variable | Description | Where to find |
|---|---|---|
| `VONAGE_API_KEY` | Your Vonage account API key | [Dashboard → API Settings](https://dashboard.nexmo.com/settings) |
| `VONAGE_API_SECRET` | Your Vonage account API secret | [Dashboard → API Settings](https://dashboard.nexmo.com/settings) |

### Required for messaging and voice

| Variable | Description | Where to find |
|---|---|---|
| `VONAGE_APPLICATION_ID` | UUID of your Vonage Application | [Dashboard → Applications](https://dashboard.nexmo.com/applications) |
| `VONAGE_PRIVATE_KEY64` | Base64-encoded RSA private key | Downloaded when creating an application; encode with `base64 -i private.key` (macOS) or `base64 -w 0 private.key` (Linux) |

### Optional, channel-specific

| Variable | Description |
|---|---|
| `VONAGE_VIRTUAL_NUMBER` | Your purchased Vonage number in E.164 format (e.g. `+12015550100`) |
| `VONAGE_WHATSAPP_NUMBER` | Your WhatsApp Business number |
| `RCS_SENDER_ID` | Your RCS agent/sender identifier |

---

## Migrating from the Kiro power

The Kiro power received your credentials from variables exported before you launched Kiro, through `${VAR}` placeholders in its `mcp.json`. The plugin reads them from its own `.env` file instead. These steps move your existing values into that file. The paths assume Kiro and a clone in the default `vonage-kiro-power` folder. In another agent client, use its plugin data directory.

1. Find your current Vonage values: your `.env` file, your shell exports, or the `env` block of `power-vonage-kiro-power-vonage-api` under `powers.mcpServers` in `~/.kiro/settings/mcp.json`. Do this first, because uninstalling the Kiro power removes that settings entry.

2. Create the plugin data directory if it does not exist:

   ```bash
   mkdir -p ~/.kiro/powers/data/vonage-kiro-power
   ```

3. Copy your existing `.env` file that holds the Vonage variables to the plugin's `.env` location:

   ```bash
   cp /path/to/your/.env ~/.kiro/powers/data/vonage-kiro-power/.env
   ```

   If that file also holds unrelated settings, keep only the Vonage lines. If your values were not in a `.env` file, create this file with one `NAME=value` line per variable from [Configuration](#configuration).

4. Restrict the file to your user:

   ```bash
   chmod 600 ~/.kiro/powers/data/vonage-kiro-power/.env
   ```

5. In Kiro's Powers panel, uninstall the Vonage power. Then install the plugin from your clone as described in [Installation](#installation) steps 1 and 2. Kiro skips an install whose name is already installed, and both use the name `vonage-kiro-power`.

6. Restart Kiro.

7. Verify the migration: ask your agent to "Set up Vonage" to run the `get-started` skill. Confirm that the credential check (`get-balance`) succeeds and that the skill moves on without showing "Your Vonage credentials are not configured yet."

> **Old exports still win.** Vonage variables that you exported before launching Kiro for the Kiro power, including variables you approved in Kiro, are still in the environment Kiro starts the `vonage-api` server with. They take precedence over the same variables in the plugin's `.env` file. Unset them and restart Kiro if you want the file to be the only source.

### If the recovery message still appears

If you completed the steps above and the skill still shows "Your Vonage credentials are not configured yet", check two things first: the folder name in the path matches the installed power's name (see [Configuration](#configuration)), and no old export overrides the file. If both are fine, your agent client probably does not expand `${PLUGIN_DATA}` yet (for example, VS Code builds affected by microsoft/vscode#335006). The server then looks for a file at the literal path `${PLUGIN_DATA}/.env`, finds nothing, and the credential check fails. To work around it:

1. Keep the plugin's `.env` file in place, so it takes over once your client supports `${PLUGIN_DATA}`.
2. Export the variables from that file in a shell, then start your agent client from that shell, for example with its command-line launcher:

   ```bash
   set -a
   . /path/to/plugin-data/.env
   set +a
   ```

   Values already in the server's environment take precedence, so the server uses them even though it cannot find the file. If your client does not pass its launch environment to MCP servers, set the variables in the client's own environment settings for the `vonage-api` server instead.

3. Run the `get-started` skill again and confirm that `get-balance` succeeds.

Remove the exports once your client expands `${PLUGIN_DATA}`.

---

## Workflows

### Get Started

Goes from credentials to a fully configured Vonage Application with a linked phone number.

**Trigger phrases:** `"Set up Vonage"`, `"Help me get started with Vonage"`, `"Create a Vonage app"`, `"Buy a number"`

**Steps:**

1. Validates your API credentials against the live API
2. Creates a Vonage Application with your chosen capabilities (Messages, Voice, or both)
3. Searches for available phone numbers in your chosen country
4. Purchases and links the number to your application
5. Outputs all environment variables you need to continue building

---

### Reports & Monitoring

Pulls activity records, visualizes data, and checks account balance.

**Trigger phrases:** `"Show my reports"`, `"Check message delivery"`, `"Monitor usage"`, `"Check my balance"`

**Steps:**

1. Retrieves message and call records for a date range you specify (up to 30 days)
2. Filters by channel, destination number, status, or direction
3. Generates charts to visualize message volume, delivery rates, and channel distribution
4. Reports your current account balance with optional low-balance warnings

---

## Available Tools

### Account

| Tool | Description |
|---|---|
| `get-balance` | Returns current account balance |

### Numbers

| Tool | Description |
|---|---|
| `search-available-numbers` | Finds purchasable numbers by country and capability |
| `purchase-number` | Purchases a phone number |
| `list-purchased-numbers` | Lists all numbers on your account |
| `link-number-to-vonage-application` | Links a number to an application |

### Applications

| Tool | Description |
|---|---|
| `create-application` | Creates a Vonage Application |
| `list-applications` | Lists all applications on your account |

### Reports

| Tool | Description |
|---|---|
| `get-records-report` | Retrieves activity records for a date range |

### Visualization

| Tool | Description |
|---|---|
| `make-chart` | Generates a chart from report data |

### Docs

The **Vonage Docs MCP Server** is always available for documentation lookups, code examples in any language, SDK references, and explanations of Vonage concepts.

Try: *"Show me a Python example for sending an SMS"* or *"What is an NCCO?"*

---

## Documentation

| Resource | URL |
|---|---|
| Vonage Developer Docs | <https://developer.vonage.com> |
| API Reference | <https://developer.vonage.com/api> |
| Vonage Dashboard | <https://dashboard.nexmo.com> |
| Vonage API Status | <https://vonageapi.statuspage.io> |
| Agent Plugins Specification | <https://github.com/agentplugins/agent-plugins-spec/blob/main/spec/1.0.0.md> |

---

## Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to participate.

---

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to uphold its standards. Please report unacceptable behavior to the project maintainers.

---

## License

Copyright 2026 Vonage

Licensed under the [Apache License, Version 2.0](LICENSE). You may not use this project except in compliance with the License. A copy is included in this repository.
