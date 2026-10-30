# Vonage CPaaS Plugin

Welcome to the Vonage plugin. This plugin gives you access to the Vonage Communications Platform APIs through guided workflows and inline documentation.

## Quick Start

Run the **Get Started** workflow to go from zero to a working Vonage application in minutes:

> **"Start the Vonage get-started workflow"** or **"Help me set up Vonage"**

The workflow will:

1. Validate your API credentials
2. Create a Vonage Application with your chosen capabilities
3. Search for and purchase a phone number
4. Link the number to your application
5. Point you to code examples in your preferred language

---

## Available Workflows

| Developer Intent | Skill | Description |
|-----------------|---------------|-------------|
| "Set up Vonage", "Get started", "Create a Vonage app", "Buy a number" | `get-started` | Complete onboarding: validate credentials, create application, purchase and link a number |
| "Show my reports", "Check message delivery", "Monitor usage", "Check my balance" | `reports-monitoring` | Pull activity records, visualize data, check account balance |

---

## Capabilities Overview

### Account Tools

| Tool | Description |
|------|-------------|
| `get-balance` | Returns the current account balance with currency code |

### Numbers Tools

| Tool | Description |
|------|-------------|
| `search-available-numbers` | Finds purchasable numbers by country code and capability (SMS, Voice) |
| `purchase-number` | Purchases a specific available number for your account |
| `list-purchased-numbers` | Lists all numbers owned by your account with linked application status |
| `link-number-to-vonage-application` | Associates a purchased number with a Vonage Application |

### Application Tools

| Tool | Description |
|------|-------------|
| `create-application` | Creates a new Vonage Application with specified name and capabilities |
| `list-applications` | Lists all Vonage Applications on your account with IDs, names, capabilities, and linked number counts |

### Reports Tools

| Tool | Description |
|------|-------------|
| `get-records-report` | Retrieves message and call activity records for a specified date range with optional filters |

### Visualization Tools

| Tool | Description |
|------|-------------|
| `make-chart` | Generates a chart from report data — supports multiple chart types |

### Documentation

The **Vonage Docs MCP Server** is always available for:

- Language-specific code examples for any Vonage API
- SDK references and installation instructions
- Webhook setup guidance
- Explanations of Vonage concepts (NCCO, JWT, Vonage Applications, Virtual Numbers, etc.)

Ask: *"Show me a Node.js example for sending an SMS"* or *"What is an NCCO?"*

---

## Useful Links

- [Vonage Developer Documentation](https://developer.vonage.com/)
- [Vonage Dashboard](https://dashboard.nexmo.com/)
- [API Reference](https://developer.vonage.com/api)
- [Vonage API Status](https://vonageapi.statuspage.io/)
- [Private Key Encoder](https://mylight.work/private-key-to-environment-variable)

## License and support

This plugin itself is covered by the [Apache-2.0](../../../LICENSE) license.

This plugin integrates with [Vonage API MCP server](https://github.com/Vonage-Community/vonage-mcp-server-api-bindings) (Apache-2.0), and [Vonage Documentation MCP server](https://github.com/Vonage/vonage-mcp-server-documentation) (Apache-2.0).

- [Privacy Policy](https://www.vonage.com/legal/privacy-policy/)
- [Support](https://www.vonage.com/support/)
