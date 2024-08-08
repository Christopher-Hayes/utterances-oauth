# utterances-oauth

This repository contains the source code for a [Cloudflare Worker](https://developers.cloudflare.com/workers/) that handles the GitHub OAuth flow and issue creation for Utterances. This script is intended to be run in the Cloudflare Worker environment. For local debugging, you can use [cloudflare-worker-local](https://github.com/gja/cloudflare-worker-local).

Let's assume your website is: `https://example.com`. The worker will be deployed at `https://api.example.com`. Those placeholders will be used in the following sections.

## Installation

To install the required packages, run:

```bash
yarn install
```

## Configuration

Create a `.env` file in the root directory with the following keys:

- **BOT_TOKEN**: A personal access token for creating GitHub issues.
  - You can create a GitHub Personal Access Token (PAT) [over here](https://github.com/settings/personal-access-tokens/new).
  - The permissions you need to provide are "Issues" with "Read and write" access.
- **CLIENT_ID**: The client ID for the [GitHub OAuth web application flow](https://developer.github.com/v3/oauth/#web-application-flow).
- **CLIENT_SECRET**: The client secret for the OAuth web application flow.
- **STATE_PASSWORD**: A 32-character password for encrypting the state in request headers/cookies. Generate one [here](https://lastpass.com/generatepassword.php).
- **ORIGINS**: A comma-delimited list of permitted origins for CORS.

### Example `.env` file

```plaintext
BOT_TOKEN=your_bot_token_here
CLIENT_ID=your_client_id_here
CLIENT_SECRET=your_client_secret_here
STATE_PASSWORD=your_32_character_password_here
ORIGINS=https://example.com,http://localhost:9000
```

### Authorization Callback URL

Your application's callback URL should be set to whatever the worker domain is plus `/authorized`. This is the URL where OAuth will redirect upon successful authorization. For example, if your worker is deployed at `https://api.example.com`, the callback URL should be:

```plaintext
https://api.example.com/authorized
```

For more information, read the [GitHub OAuth documentation](https://developer.github.com/apps/building-oauth-apps/authorization-options-for-oauth-apps/).

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

Before deploying, ensure that your `.env` file contains the necessary `CLOUDFLARE_*` entries. Refer to the [@cfworker/dev README](https://www.npmjs.com/package/@cfworker/dev) for more details.

To deploy the application, execute:

```bash
yarn run deploy
```
