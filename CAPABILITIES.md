# Component and operation coverage

The runtime matrix is available in **Inspect Metadata → Component capabilities**. It combines the backend native reader/mapping evidence with the same file-adapter allowlist used by actual operations (`component-operation-capabilities.js`). Unknown/conditional coverage remains explicit; a readable row is not proof of an isolated deployment or deletion mapping.

| File adapter | Operations | Exact scope | Evidence |
| --- | --- | --- | --- |
| Azure Function App | diff, deploy | Selected script files, or one complete compiled application. Readable source comparison uses the explicitly supplied baseline, not recovered Target source. Replaced Target files have recovery capture. | `function-project.js`; function-project.test.js, function-project-routing.test.js |
| Azure Bicep template | diff, deploy | One exact template and reviewed Incremental deployment. No generic Azure resource Delete, Retrieve or recycle adapter. | `azure-file-deployment.js`; azure-file-deployment.test.js, azure-file-routing.test.js |
| Standard Logic App workflow | diff, deploy | One exact workflow definition. App-wide configuration and other workflows stay outside the selected payload. | `azure-file-deployment.js`; azure-file-deployment.test.js, azure-file-routing.test.js |
| Azure app settings | diff, deploy | Checked keys merge into freshly read Target settings. Secret values stay in private review; no independent Delete or Retrieve command. | `azure-app-configuration.js`; azure-app-configuration.test.js, azure-file-routing.test.js |

Native Dataverse solution files and Power Pages files support supported exact-file Diff/Retrieve/Deploy/Delete paths after their type, identity and recovery checks. A selected PCF project represents a complete control; retrieving compiled output does not recreate authored source. Live component mappings are reported by the backend instead of duplicated in this document.

App and Power Pages solution destinations are independent. File context actions use only checked authored files, while a selected live metadata component represents its complete mapped component. Exact native deletion is permitted only after verified recovery capture; immutable audit/system fields and independently recreated records remain outside recovery.

All external mutations require manual review. This matrix establishes local adapter coverage, not successful tenant deployment. Use [SUPPORT.md](SUPPORT.md) for the manual service matrix.

## Independent record tools

- **Profile Data**: Profiles an explicitly bounded sample with required-field, alternate/custom-key and lookup-reference rules. These validation rules are independent of viewing or editing Query Data results.
- **Inspect Record Audit History**: Prefer Query Data result row → Audit history when the record is loaded. Direct access supports a known table/GUID without first running a query.
- **Edit Record Relationships**: Prefer Query Data result row → Related memberships when the record is loaded. Direct access supports a known table/GUID and exact N:N membership review without first loading query results.
- **Transfer File and Image Columns**: Prefer Query Data result row → Files and images when the record is loaded. Direct access supports a known table/GUID and binary download/upload, which scalar grid editing does not provide.
