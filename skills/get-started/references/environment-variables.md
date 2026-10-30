# Environment Variables

Configure these variables in the `.env` file in this plugin's data directory (one `NAME=value` per line) before running workflows, then restart your agent client.

## Account-Level Credentials (required for all workflows)

| Variable | Description | Where to Find |
|----------|-------------|---------------|
| `VONAGE_API_KEY` | Your Vonage account API key | [Vonage Dashboard → API Settings](https://dashboard.nexmo.com/settings) |
| `VONAGE_API_SECRET` | Your Vonage account API secret | [Vonage Dashboard → API Settings](https://dashboard.nexmo.com/settings) |

## Application-Level Credentials (required for messaging and voice)

| Variable | Description | Where to Find |
|----------|-------------|---------------|
| `VONAGE_APPLICATION_ID` | UUID of your Vonage Application | [Vonage Dashboard → Applications](https://dashboard.nexmo.com/applications) |
| `VONAGE_PRIVATE_KEY64` | Base64-encoded RSA private key for JWT authentication | Downloaded when creating an application — encode with [this tool](https://mylight.work/private-key-to-environment-variable) |

## Channel-Specific Variables (optional, required for specific channels)

| Variable | Description | Where to Find |
|----------|-------------|---------------|
| `VONAGE_VIRTUAL_NUMBER` | Your purchased Vonage number in E.164 format (e.g. `+12015555555`) | [Vonage Dashboard → Numbers](https://dashboard.nexmo.com/your-numbers) |
| `VONAGE_WHATSAPP_NUMBER` | Your WhatsApp Business number | Vonage Dashboard → Messages sandbox or approved WhatsApp number |
| `RCS_SENDER_ID` | Your RCS agent/sender identifier | Vonage Dashboard → Messages → RCS |
