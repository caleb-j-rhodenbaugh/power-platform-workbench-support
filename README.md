# Power Platform Workbench

**Review the changes before you deploy.** Power Platform Workbench brings component diffing, deployment, data queries and diagnostics into Visual Studio Code for your own Power Platform and Azure projects.

This is the public home for bug reports, feature requests and development tracking. Extension source development is maintained in a separate private repository. Do not upload project source, client information or business data here.

[Report a bug or request a feature](https://github.com/caleb-j-rhodenbaugh/power-platform-workbench-support/issues/new/choose) · [Browse issues](https://github.com/caleb-j-rhodenbaugh/power-platform-workbench-support/issues) · [Development boards](https://github.com/users/caleb-j-rhodenbaugh/projects/4) · [Donate](https://gofund.me/32ffe41af)

## Diff first, then deploy

1. Open **Accounts** in the Workbench pane. Sign in to the relevant service and select your environment, solution and Azure resource defaults.
2. In Explorer, right-click the exact local files or package manifest and choose **Power Platform Workbench: Diff**.
3. Inspect the changes in **Diff Review**. Uncheck files that are not ready and verify the captured destination.
4. Select **Deploy** and review the confirmation. Use **Retrieve** when you intend to replace selected local files with their Target versions.

Power Apps components and Power Pages components use separate solution selections. Azure projects retain their reviewed app destination. Component coverage depends on the type and operation; a readable metadata row does not imply that it can be deployed or deleted independently.

| Workbench area | What it supports |
| --- | --- |
| Component changes | Review supported Dataverse, Power Pages, PCF, Function App, Standard Logic App, Bicep and app-setting changes before deployment. |
| Query Data | Build queries with columns, filters, related tables and summaries; review URL, JSON and native XML representations; work with returned records. Some definitions require coordinated requests or Workbench calculations. |
| Data import | Map local data, review changed records, and import only the selected rows. |
| Metadata and diagnostics | Inspect solution components, workflow runs and available Azure telemetry using the selected accounts. |
| Local development | Compare and merge text, scaffold supported components and preview local browser content. Server-side behavior is not emulated. |
| Agent access | MCP-compatible agents can use structured background sessions and reveal their current state for a human handoff. Authentication and remote writes remain human actions. |

Compiled Function Apps and authored PCF projects have complete deployment-unit boundaries. Unmanaged PCF publication can publish all pending customizations in the Target environment; read its confirmation before proceeding. An incomplete or uncertain write is not retried automatically.

## Installation and prerequisites

Marketplace publication is being prepared. The intended listing is [Power Platform Workbench by Caleb-J-Rhodenbaugh](https://marketplace.visualstudio.com/items?itemName=Caleb-J-Rhodenbaugh.power-platform-workbench); it becomes available after publication.

The verified release target is **Windows x64 desktop VS Code**, with VS Code **1.102.0 or later** and **PowerShell 7.5 or later**. Install the Microsoft Power Platform CLI for Power Platform operations and Azure CLI for Azure operations. Building a selected project also requires its declared SDKs and dependencies. Use a trusted local workspace. Browser-hosted, virtual and remote/container workspaces are not advertised as verified deployment targets.

## Report an issue

Use the [issue forms](https://github.com/caleb-j-rhodenbaugh/power-platform-workbench-support/issues/new/choose). Include the extension and host versions, the tool or action, reproducible steps, and a sanitized expected-versus-actual result. Use a small synthetic example when possible.

Issues, comments and attachments are public. Remove account names, organization names, tenant/subscription IDs, environment URLs, local paths, record data and customer content. Never include tokens, cookies, passwords, connection strings, signed callback URLs, export packages or credential caches. Check screenshots as carefully as text.

If an operation may have reached its Target, report that uncertainty. Inspect the existing outcome before retrying; do not repeatedly deploy or import just to produce a report.

See [contribution guidance](CONTRIBUTING.md) and the separate [security reporting policy](SECURITY.md). Issue status and project boards communicate planning, not delivery guarantees or a support SLA.

## Support development

If Workbench is useful to you, [make a donation](https://gofund.me/32ffe41af). Donations are optional and do not change support priority or grant access to private source.

## License

Documentation and issue templates in this repository are provided under the [MIT license](LICENSE). This repository does not contain the extension's implementation or any user's project files.
