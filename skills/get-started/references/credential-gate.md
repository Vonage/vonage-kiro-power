# Onboarding

Before running any workflow, verify that the required credentials are available.

## Step 1: Check account-level credentials

Call `get-balance` using the `vonage-api` MCP server.

- If the call **succeeds**, credentials are configured correctly. Proceed to the requested workflow.
- If the call **fails** with an authentication error (401) or returns an empty/zero result that looks unexpected, the credentials are missing or wrong. Stop immediately and follow the recovery steps below.

**CRITICAL**: Do not attempt any other workflow steps until credentials are confirmed working. Proceeding without valid credentials will cause all subsequent API calls to fail.

## Step 2: Recovery — credentials not configured

If `get-balance` failed, display this message to the user exactly:

> **Your Vonage credentials are not configured yet.** To fix this, add the following to the `.env` file in this plugin's data directory (the plugin README shows where your agent client keeps it):
>
> ```text
> VONAGE_API_KEY=your_api_key_here
> VONAGE_API_SECRET=your_api_secret_here
> ```
>
> You can find both values at [https://dashboard.nexmo.com/settings](https://dashboard.nexmo.com/settings).
>
> Once you have saved the file, **restart your agent client** so the `vonage-api` server picks up the new environment variables, then try again.

Do not continue until the user confirms they have saved the `.env` file in the plugin's data directory and restarted their agent client. Then re-run `get-balance` to confirm before proceeding.
