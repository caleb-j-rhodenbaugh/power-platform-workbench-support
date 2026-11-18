# Privacy and local data

Power Platform Workbench runs in the VS Code extension host and uses the accounts, environments, websites and Azure resources that you select. It does not operate a publisher-hosted service. This document describes the extension's own behavior; Microsoft services, installed command-line tools and any connected AI client have their own data policies.

## Distribution contents

The VSIX contains extension code, generic backend tooling, public documentation and licensed dependencies. It does not include the developer's application/website source, workspace configuration, account caches, deployment manifests, exports, recovery copies or business records. The standalone source export includes synthetic tests, not a copy of the enclosing project. Internal implementation trackers, project handoffs and review reports are excluded. Both distribution paths run the same privacy check over their exact output before reporting success.

Runtime destinations and project inputs come from the user's selected workspace, accounts and resources. Public command and configuration identifiers belong to Workbench; no bundled customer identifier determines a destination. The extension's published author and publisher attribution are public product metadata, not credentials or a default author for generated projects. An earlier local extension with a different installed identity keeps its own storage; this package does not automatically adopt its accounts, history or recovery data.

## Network access

Workbench uses Microsoft authentication and the selected Dataverse, Power Pages, Azure and related development service endpoints to carry out requested operations. Local command-line tools, including Power Platform CLI, Azure CLI, PowerShell and .NET, may make their own requests. Building a project can restore its declared dependencies. Opening documentation, a maker portal or an Azure portal opens that external service in your browser.

No publisher analytics or telemetry sender is included in the authored extension runtime. Workbench's performance measurements are bounded in-memory records of phase, duration, count and outcome. This does not disable telemetry that VS Code or separately installed tools provide under their own settings.

## Accounts and credentials

- Saved account names, tenant/cloud identifiers and selected environments are retained in VS Code's local extension state. Azure subscription, resource group, Standard Logic App and Power Pages solution choices are retained for their selected workspace and account identity.
- Dataverse and Azure browser sign-in use separate CLI cache directories under the extension's local global storage. Their directories are restricted to the local user. PAC profiles remain managed by Power Platform CLI.
- Native VS Code authentication sessions use VS Code SecretStorage for tokens. Tokens also exist in process memory while needed. They are not ordinary settings values or entries in a package manifest.
- An account saved locally is not proof of a currently valid session. Accounts shows saved connections separately from active sessions. Sign out through Accounts or the corresponding Workbench provider in VS Code Accounts to revoke Workbench's local session and clear that connection's private cache. This does not sign you out of unrelated Microsoft sessions or revoke service-side grants.

## Queries, reviews and recovery copies

The [shared evidence contract](EVIDENCE.md) describes ownership, full-result access and each store's retention controls. Saved work is not automatically expired by the recycle-bin setting.

| Data | Location and lifetime |
| --- | --- |
| Saved/recent query definitions | Local workspace state. Definitions include query literals and environment identity; the recent list retains up to six entries. Saved queries remain until removed. |
| Query and diagnostic result history | Captured results are stored locally in workspace/account/environment/solution-bound extension records across restarts. These copies may contain business data and query literals. History in the owning tool restores read-only evidence; Clear result history removes its captures. There is no automatic expiry. Active editable grids and reviewed mutations remain separate sessions. |
| Saved tool selections | Save selections stores editable form values locally for the captured workspace/account/environment/solution. Password, hidden authority and read-only fields are excluded. Saved selections restores values against the current form without submitting. Clear saved selections removes these records. Query Data retains its separate saved-query controls. |
| Review state and temporary artifacts | Exact file/record review and editable authority remain with their running session. Workflows also create temporary files for CLI input/output, diffs and private review. Restoring result history does not restore executable callbacks or mutation authority. Diff Content does not retain history. |
| Exports and retrieved source | The local destination chosen or shown by the workflow. These files can contain business data, component definitions and configuration. They remain until you remove them. |
| Component Recycle Bin | Local extension storage contains recovery definitions and captured writable data, including binary files where applicable. Default retention is 30 days; change **Recycle bin retention (days)** in Workbench Settings. Automatic expiration checks run at startup, hourly and after retention-setting changes for currently open, trusted local workspace folders. Copies belonging to a closed workspace can remain past expiry until that workspace is opened again. Cleanup is deferred during an active recovery operation and preserves questionable artifacts. Expiration affects recovery copies, not live components. |
| Diagnostics | A bounded VS Code output channel retains operation summaries; performance details remain in memory. Retained server job IDs and environment identities let the user resume monitoring without resubmitting a write. |
| Migration preferences | An explicitly chosen new local JSON file contains allowlisted preferences, sidebar layout, workspace/storage paths and hashes/references for verified recovery files. Import retains these references in workspace state. Accounts, secrets, saved query definitions and recovery payloads are excluded. The file and references remain until removed; do not publish private workspace paths. |

Temporary-file cleanup runs after operations and retries some file-lock failures. Closing VS Code, process failure or persistent file locks can leave files behind; Workbench does not promise that every local artifact is erased on exit. Uninstalling an extension is not a secure data-erasure process. Protect the operating-system profile, workspace, temporary directories and any backed-up/exported files according to the sensitivity of your data.

## AI and MCP access

In a trusted local workspace, Workbench registers its MCP server with VS Code and automatically configures its owned entry in `.codex/config.toml` and `.cursor/mcp.json`. Existing unrelated or custom entries are preserved. Global client configuration is inspected for conflicts but is not rewritten. The entries contain the local executable/script and connection-file paths, not a Dataverse or Azure token.

The MCP bridge uses local stdio and a private authenticated named pipe or Unix socket. Its connection file under extension storage contains a temporary local bearer secret; treat it as a credential and do not share or commit it. **Stop Agent Access** stops the bridge for the workspace. An external client needs the workspace open in VS Code to use that bridge.

A connected agent can read tool descriptions, context, fields, query data, result rows and Workbench-produced documents through the supported interfaces. Full results are available through paging; visual paging is not a data-access boundary. Secret fields and authentication sessions are excluded from agent results. External mutations and sign-in remain manual actions; an agent can prepare a review and reveal it to the user.

Data returned by Workbench to an AI client is then subject to that client's storage and model-provider policies. Connecting an agent authorizes disclosure of the queried or reviewed data to that client. Workbench cannot control what the client stores or sends onward. Only connect clients approved for the data you intend to inspect.

## Sharing diagnostics

Do not attach tokens, signed callback URLs, private app settings, connection files, recovery packages or customer/business records to public bug reports. Inspect exported files and screenshots before sharing them. Sanitized operation summaries are preferable to raw process output. Use the [public issue form](https://github.com/caleb-j-rhodenbaugh/power-platform-workbench-support/issues/new/choose) for sanitized product defects and suggestions. Use [private vulnerability reporting](https://github.com/caleb-j-rhodenbaugh/power-platform-workbench-support/security/advisories/new) for a suspected vulnerability, following [SECURITY.md](SECURITY.md).
