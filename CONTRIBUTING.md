# Contributing to the Vonage CPaaS Agent Plugin

Thank you for your interest in contributing! This document explains how to participate in the development of the Vonage CPaaS Agent Plugin, an [Agent Plugins 1.0.0](https://github.com/agentplugins/agent-plugins-spec/blob/main/spec/1.0.0.md) package that works in any compatible agent client.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Ways to Contribute](#ways-to-contribute)
- [Reporting Bugs](#reporting-bugs)
- [Suggesting Enhancements](#suggesting-enhancements)
- [Development Setup](#development-setup)
- [Making Changes](#making-changes)
- [Pull Request Process](#pull-request-process)
- [Style Guide](#style-guide)
- [Questions](#questions)

---

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to uphold these standards. Please report unacceptable behavior to the project maintainers.

---

## Ways to Contribute

| Type | Description |
|---|---|
| **Bug reports** | Something not working as expected? Open an issue. |
| **Workflow improvements** | Ideas to make the guided workflows clearer or more robust. |
| **New skills** | Propose a new developer workflow (e.g., for a Vonage API not yet covered). |
| **Documentation fixes** | Typos, unclear instructions, broken links. |
| **Tool additions** | New MCP tool bindings that would benefit Vonage developers. |

---

## Reporting Bugs

Before opening a bug report, check the existing [issues](../../issues) to avoid duplicates.

When filing an issue, include:

1. **What you did** — the prompt or action you took
2. **What you expected to happen**
3. **What actually happened** — include any error messages from your agent client or the MCP server
4. **Your environment:**
   - Agent client name and version
   - Operating system
   - Node.js version (`node --version`)

---

## Suggesting Enhancements

Open an issue with the label `enhancement` and describe:

- The developer task or problem you want to address
- The workflow or tool you are proposing
- Why this would be useful to other Vonage developers

---

## Development Setup

This repository contains configuration and skill files — there is no build step. You only need:

1. An agent client that supports Agent Plugins 1.0.0 to test the plugin locally (for example, [Kiro](https://kiro.dev))
2. A [Vonage API account](https://ui.idp.vonage.com/ui/auth/registration) with credentials

**Clone the repo:**

```bash
git clone https://github.com/Vonage/vonage-kiro-power.git
cd vonage-kiro-power
```

**Install the plugin:**

Install the plugin in your agent client from your local clone. In Kiro, for example, open the Powers panel, add a custom power, and choose **Import power from a folder**. Kiro copies the folder when it installs the power, so reinstall it after each change.

**Configure credentials:**

Add your credentials to the `.env` file in the plugin's data directory, then restart your agent client, as described in [README.md](README.md#configuration).

> **Never commit credentials.** All secrets must be supplied via environment variables, never hardcoded in files tracked by git.

**Test your changes:**

Trigger the affected skill with a natural language prompt. Verify that the skill guides the AI correctly at each step.

---

## Making Changes

### Skills (`skills/`)

Each directory in `skills/` holds one `SKILL.md` that encodes a single developer workflow as a set of instructions for the AI model. Shared reference content lives in `skills/get-started/references/`. When editing:

- Keep steps numbered and in order — the AI follows them sequentially.
- Every error case should have a specific, actionable message.
- Link to the relevant Vonage Dashboard or documentation page wherever a developer might need to take manual action.
- Do not assume a particular programming language in examples — always prompt the developer to specify their preference.

Every `SKILL.md` must also follow the [Agent Skills specification](https://agentskills.io/specification):

- The frontmatter `name` matches the directory name.
- The frontmatter `description` is at most 1024 characters and includes trigger phrases.
- The body stays under 500 lines.
- Every supporting file is linked directly from `SKILL.md` with a relative path.

### `plugin.json`

This is the Agent Plugin manifest. Keep the `keywords` list current with the capabilities the plugin covers so that agent clients can match user intent correctly. The manifest accepts only these top-level fields: `$schema`, `name`, `version`, `description`, `author`, `homepage`, `repository`, `license`, `keywords`, and `extensions`.

### `mcp.json`

This file defines the MCP server configuration. The `vonage-api` server's `env` carries only two non-secret dotenv settings: `DOTENV_CONFIG_PATH`, set to `${PLUGIN_DATA}/.env`, and `DOTENV_CONFIG_QUIET`, set to `"true"`. Together they point the server at the `.env` file in the plugin's data directory. Never add credentials or Vonage environment variables to `env`: Agent Plugins forbids secrets there, and a variable set in `env` hides the value in the `.env` file. Environment variable names must stay in sync with those documented in `skills/get-started/references/environment-variables.md` and `README.md`.

---

## Pull Request Process

1. **Fork** the repository and create a branch from `main`:

   ```bash
   git checkout -b feat/your-feature-name
   ```

2. **Make your changes** and test them locally in an agent client.

3. **Commit** with a clear, concise message following [Conventional Commits](https://www.conventionalcommits.org/):

   ```text
   feat: add skill for number porting workflow
   fix: correct error message for 402 in get-started
   docs: update VONAGE_PRIVATE_KEY64 encoding instructions
   ```

4. **Open a pull request** against `main` with:
   - A summary of what changed and why
   - Steps to reproduce and test the change in an agent client
   - References to any related issues (use `Closes #123`)

5. Address any feedback from maintainers and keep your branch up to date with `main`.

6. Once approved, a maintainer will merge your PR.

---

## Style Guide

### Skills

- Use Markdown headings (`##`, `###`) to structure steps.
- Use `>` blockquotes for all user-facing messages produced by the AI.
- Use code formatting (backticks) for environment variable names, API field names, and tool names.
- Keep error messages specific: name the variable, field, or step that failed.
- Avoid vague phrases like "it might not work" — be prescriptive.

### Commit messages

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

| Prefix | Use for |
|---|---|
| `feat:` | New workflow, tool, or capability |
| `fix:` | Correcting a broken workflow step or error message |
| `docs:` | README, CONTRIBUTING, or inline documentation changes |
| `chore:` | Maintenance — config tweaks, dependency bumps |
| `refactor:` | Restructuring without behavior change |

---

## Questions?

Open an issue or start a [GitHub Discussion](../../discussions). We are happy to help.
