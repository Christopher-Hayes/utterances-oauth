# utterances-oauth

This repository contains the source code for a [Cloudflare Worker](https://developers.cloudflare.com/workers/) that handles the GitHub OAuth flow and issue creation for Utterances. This script is intended to be run in the Cloudflare Worker environment. For local debugging, you can use [cloudflare-worker-local](https://github.com/gja/cloudflare-worker-local).

Let's assume your website is: `https://example.com`. The worker will be deployed at `https://api.example.com`. Those placeholders will be used in the following sections.

## Installation

To install the required packages, run:

```bash
yarn install
```

## GitHub App Configuration

1. Create a new GitHub App [here](https://github.com/settings/developers). (A GitHub App, not an OAuth App.)
2. Set the following fields:
   - **Homepage URL**: `https://example.com`
   - **Callback URL**: `https://api.example.com/authorized`
   - **Callback URL**: `https://localhost:1234/authorized` (for local testing)
   - [X] **Request user authorization (OAuth) during installation**
   - Permissions:
     - **Repository permission**:
       - [X] **Issues**: Read & write
3. When you're done creating the app, note the **Client ID**. Go to "Install App" on the left, and install the app on yourself or the owner of the repositories you want to use for comments.

## Configuration

Rename the `.env.sample` file to `.env` and fill in the the values for the following keys:

- **BOT_TOKEN**: A personal access token for creating GitHub issues.
  - You can create a GitHub Personal Access Token (PAT) [here](https://github.com/settings/tokens?type=beta).
  - The permissions you need to provide are "Issues" with "Read and write" access. Ensure this token has access to the repositories you want to use.
- **CLIENT_ID**: The client ID for the GitHub OAuth web application flow.
  - Obtain these from your GitHub App [setup](https://github.com/settings/developers).
- **CLIENT_SECRET**: The client secret for the OAuth web application flow.
- **STATE_PASSWORD**: A 32-character password for encrypting the state in request headers/cookies. Generate one [here](https://lastpass.com/generatepassword.php).
- **ORIGINS**: A comma-separated list of allowed origins for CORS.
  - If your site uses `www`, you must include it in the list below to avoid CORS errors.
  - For example: `https://www.example.com,https://example.com,http://localhost:3000`
- **CLOUDFLARE_API_KEY**: The API key for your Cloudflare account.
  - You can create a new token under "Manage API tokens" in Cloudflare's Worker section.
- **CLOUDFLARE_ZONE_ID**: The Zone ID for your domain. Found on the DNS page.
- **CLOUDFLARE_ACCOUNT_ID**: The Account ID for your domain. Found on the DNS and Workers pages.
- **CLOUDFLARE_WORKERS_DEV_PROJECT**: The name of your Cloudflare worker project, found under "Manage" in your Cloudflare Workers.

## Development

To customize the deployment script based on your own configuration, update the `deploy` script in `package.json` with your Cloudflare Worker details. Modify the following parts:

- **--name**: Replace `utteranc-es` with your desired worker name. This will be the identifier for your deployed Cloudflare Worker.
- **--route**: Replace `'api.example.com/*'` with your desired route. This specifies the domain and path where your Cloudflare Worker will be accessible.

Here is the original script for reference:

```json
"deploy": "cfworker deploy --name utteranc-es --route 'api.example.com/*' src/index.ts"
```

## Running Locally

To start the local development server, execute:

```bash
yarn run start
```

## Build

To compile the project, run:

```bash
yarn run build
```

## Deployment

To deploy the application, execute:

```bash
yarn run deploy
```
