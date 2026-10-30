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

2. Install the plugin in your agent client from the cloned folder. Your client discovers the skills in `skills/` and the MCP servers in `mcp.json`.

   - For example, in Kiro, open the Powers panel, add a custom power, and choose **Import power from a folder** (pick the cloned folder) or **Import power from GitHub** (paste the repository URL). See [Install powers](https://kiro.dev/docs/powers/installation/) for the current steps.

3. Add your Vonage credentials to the plugin's `.env` file, which sits directly inside the plugin data directory. The plugin data is stored in `${PLUGIN_DATA}` (**neither** in your project root, **nor** in the plugin install folder). For example, in Kiro it is `~/.kiro/powers/data/vonage-kiro-power/.env` and in VS Code with Copilot it is `~/.copilot/plugin-data/_direct/<<HASH_VALUE>>/.env`:

   ```text
   VONAGE_API_KEY=your_api_key_here
   VONAGE_API_SECRET=your_api_secret_here
   ```

   For a template, see `.env.example`. Find both values at [Vonage Dashboard → API Settings](https://dashboard.nexmo.com/settings).

4. Restart your agent client so the `vonage-api` server starts with the new values.

---

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

### Common issues

#### PLUGIN_DATA expansion

If you completed the steps above and the skill still shows "Your Vonage credentials are not configured yet", your agent client probably does not expand `${PLUGIN_DATA}` yet (for example, VS Code builds affected by [microsoft/vscode#335006](https://github.com/microsoft/vscode/issues/335006)). The server then looks for a file at the literal path `${PLUGIN_DATA}/.env`, finds nothing, and the credential check fails. To work around it:

1. Keep the plugin's `.env` file in place, so it takes over once your client supports `${PLUGIN_DATA}` (see [Agent Plugins §9.1](https://github.com/agentplugins/agent-plugins-spec/blob/main/spec/1.0.0.md#91-subprocess-environment)).
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
