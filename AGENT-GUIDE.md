# Power Platform Workbench agent guide

Use Workbench's MCP tools to discover capabilities, prepare reviews, query records and inspect diagnostics through structured background sessions. A tool opening successfully is the start of the workflow, not its result. Continue the returned session until you have read its results or reached a step that requires the user.

This guide describes Workbench's interface. Follow the user's instructions and the current repository's agent instructions as well. External deployment, publication, import, deletion, other remote writes, account changes and credential entry remain manual user operations. Showing a page does not authorize or execute those actions.

## Connect and discover

Keep the trusted local workspace open in the Workbench host. Automatic setup registers Workbench with supported VS Code hosts and maintains its owned project entries for Codex and Cursor. Other MCP clients can use the transport configuration provided by **Settings → Agent Access → Configure Agent Access**. Client trust and approval policies still apply. Setup does not sign in to Azure or Dataverse. See [connection setup](WORKFLOWS.md#connect-an-ai-or-si-agent).

1. Call `workbench_catalog` with `{ "detail": "index" }` to discover the current tools and their availability. For a smaller initial response use `compact`; request details before launching.
2. Call `workbench_catalog` with `{ "tool": "<returned tool ID or name>" }` to read its purpose, prerequisites, effects, input schema and included workflows. MCP `tools/list` also publishes each launchable tool's input schema. Do not guess names from old commands or screenshots.
3. Call `workbench_context` with `{ "action": "read" }`. In a multi-root workspace, supply the exact open root in `workspace`. Verify Source, Target, account and solution against the user's intended scope.
4. If the context operation returns a `sessionId`, continue that session. Its completed snapshot contains `context`; do not repeat the context operation while it waits for authentication or discovery.
5. If the task requires changing Source, Target or solution, use `workbench_context` with `action: "select"`, a returned `role`, `id` and `contextKey`. Read the resulting context before continuing. Never substitute another account, environment or solution when access fails.

Catalog availability explains prerequisites; it is not proof of authentication or authorization. Dataverse and Azure use separate accounts. A saved connection is not an active session. Accounts holds environment profiles, Source/Target selections, Azure resource defaults and the Target-bound Power Pages solution. Changing Target restores its saved local preferences; read the returned context again before continuing. The app solution and the Power Pages solution are distinct destinations. Tool pages do not have context headers; the structured context interface remains available.

## Choose the owning tool

These are common entry points; use the catalog for the complete current list and each tool's input schema.

| Task | Entry point and scope |
| --- | --- |
| Query records or inspect editable results | `workbench_queryData`; one base table and its related records, using the existing Query Data controls and URL/JSON/XML editor. |
| Diff local component files, a folder or manifest against Target | `workbench_fileDiff`; exact absolute workspace paths. The retained Diff Review owns retrieval and deployment actions. |
| Inspect components, schema, dependencies or solution context | `workbench_compare` (**Inspect Metadata**); select its returned workflow and component IDs. |
| Prepare a data import | `workbench_modifyData` (**Import Data**) or `workbench_fileDiff` for supported data files; review record differences and the selected subset before human import. |
| Compare or merge local text | Discover **Diff Content**; this is separate from file-to-Target component comparison. |
| Inspect flow runs or Azure telemetry | Discover **Flow Explorer** or **Azure Diagnostics** and choose the matching returned workflow/resource choices. Power Automate and Azure Logic Apps are different sources. |
| Reopen saved work, history or recovery evidence | Discover the owning tool and use its returned saved-item or result actions. Captured history is not proof of current Target state. |

Consolidated workflows and result actions are not additional launchers. When a catalog entry has `execution: "interaction"`, operate its existing session/result rather than launching a new tool. [Command access](COMMAND-ACCESS.md) maps human commands to their owning tools; [capabilities](CAPABILITIES.md) records component coverage and scope.

## Continue a session

Launch the discovered tool once. Use `mode: "background"` (the default) for structured operation without a tab, or `mode: "interactive"` for the same API with a visible tool page. `workspace` must identify an exact open workspace root when more than one is open. Optional launch `values` submit only the first ordinary form; they never accept later confirmations.

Read the response's `structuredContent`, or the equivalent JSON text when the client exposes only text. Retain its `sessionId`. Surface IDs come from `surfaces[].id`; form `promptId` and `revision` come from that surface's current prompt. Field IDs, available choices, validation and result descriptors are dynamic.

| Returned state | Next step |
| --- | --- |
| `running` | Read the same session, optionally with `waitMs` (up to 10,000). Do not relaunch or repeat the pending action. |
| `awaitingInput` | Read the current form or prompt and inspect other surfaces for ready results. Persistent forms such as Query Data stay open after execution; available results do not require another submission. Set or submit inputs only when the requested work still needs them. |
| `ready` | Inspect retained results and available actions. Read every required result page; the visible preview may be incomplete. |
| `manualRequired` | Read the reason. Show the session for authentication, secret entry or another human-only step; do not bypass it. |
| `failed` | Read the error, validation and retained evidence. Report the actual failure; do not represent it as an empty successful result. |
| `completed` | Inspect the final context/results/outcome. Completion of a process does not by itself establish a successful remote change. |
| `closed` | The session is retired. Do not reuse its IDs. |

Use `workbench_session` for both background and interactive sessions:

- `read` returns current state; supplying `resultId` reads a retained result page.
- `set` patches `values` without submitting the final form. Set dependent selectors in order, then read the refreshed fields and choices. For example, selecting a table makes its column choices available.
- `submit` validates and runs the current ordinary form. Supply its current `surfaceId`, `promptId` and `revision`, including any desired `values`.
- `answer` handles a returned ordinary native prompt with its returned `choiceIds`, `value` or `paths`, as applicable. Never answer credential prompts or final remote-write confirmations.
- `action` invokes a returned `actionId`, with its `resultId` or `embeddedId` when required. Generic results can instead expose `rowAction` metadata without an ID: invoke that with `action: "action"`, `surfaceId`, `resultId` and the original retained `row` index, omitting `actionId`. Other surfaces can return row actions with their own IDs; follow that surface's contract. Never substitute a displayed page's row position for the original retained index.
- `show` displays the retained session without rerunning it. `release` hands visible pages to the user; see the handoff section below.
- `cancel` stops a pending prompt or cancellable operation. Cancellation does not undo completed work. `close` disposes agent-owned pages and retained results when no handoff is needed.

After every set, submit, answer or action, inspect the response and use the new revision. Never guess dynamic field names, fabricate component IDs, reuse a stale revision, or send an arbitrary command or webview message. If a requested action is not returned or is disabled, read the stated prerequisite instead of circumventing it.

### Example: query and hand results to the user

The examples below use placeholders for values returned by the running tool. Replace them with the current IDs and numeric revision; they are not values to send literally.

1. Discover `workbench_queryData`, verify context, then launch it with `{}` in a single-root workspace.
2. Inspect the Table choices, then call `workbench_session`:

   ```json
   {
     "sessionId": "<returned sessionId>",
     "action": "set",
     "surfaceId": "<returned surface id>",
     "promptId": "<current prompt id>",
     "revision": 1,
     "values": { "table": "<returned table option value>" }
   }
   ```

3. Read the refreshed column, filter and query fields. Set the user's requested columns and conditions using their returned field IDs. Submit the current form with its new revision. Existing query validation/conversion applies; do not create a second query that discards the visual selections.
4. While the operation is running, read that same session. Inspect every surface: Query Data's persistent form can keep the overall session `awaitingInput` even when its separate query-results surface is `ready`. Once that result surface is ready and execution is no longer in progress, read complete retained results and follow any explicitly returned server-page action needed for the requested scope. Do not wait for the overall session to become `ready` or resubmit merely because the form is still open.
5. Show the completed session, then release it so the user can edit the existing result grid. Do not rerun the query merely to make its results visible.

## Read all evidence

Use the result descriptor's actual ID and page through retained rows:

```json
{
  "sessionId": "<returned sessionId>",
  "action": "read",
  "surfaceId": "<result surface id>",
  "resultId": "<returned result id>",
  "offset": 0,
  "limit": 100
}
```

Repeat with the returned `nextOffset` until it is `null`. This pages through evidence already retained by Workbench; it does not automatically fetch every record from the service. Query `hasMore`, `incomplete`, coverage warnings and returned Next actions describe separate server pagination. Read those states, invoke the returned read-only continuation when needed, and do not claim complete coverage while a continuation or partial-read error remains.

Use `workbench_documents` with `{ "action": "list" }` to find captured editor/diff documents. Read each relevant `documentId` with `action: "read"`, `offset` and `limit`, following `nextOffset` until `null`. For a diff, inspect both Reference and Local documents. If the file comparison is opened through a result-row action, invoke that returned action first. Editable documents require their exact current `version`; `action: "edit"` leaves changes unsaved for the existing workflow's validation. This interface cannot read arbitrary editor files by path.

`workbench_surfaces` locates sessions and visible pages when needed. `workbench_read` and `workbench_interact` support explicitly interactive controls; prefer `workbench_session` for the retained structured workflow. Local UI Preview's background HTML is evidence of generated content, not a rendered visual test. Specialist applications and unsupported visual interactions remain explicit handoffs.

## Review exact changes before deployment

For a local file or manifest review, discover `workbench_fileDiff` and pass the absolute workspace path of the requested selection. Write file manifests as `{ "paths": ["relative/file"] }`. Resolve entries in `.vscode/power-platform-package.json` from the workspace root, named solution manifest entries from the unpacked solution source root, and named website manifest entries from the downloaded site root. Existing named manifests with an array of `{ "path": "…" }` entries remain supported. Preserve the requested files and the user's checked subset throughout review.

Read every changed review row and its status, captured destination, comparison and selection state. Open row comparisons through returned actions. Identical files are omitted from deployment selection. A new component may bring its exact paired descriptor into the checked review under Workbench's existing rules; review that visible addition. Do not add siblings, dependencies, dirty editors or unrelated local files yourself.

A checked live metadata row represents a complete component, whereas a local file action represents its exact selected files. Compiled PCF controls, compiled Function Apps and infrastructure templates have their documented complete deployment scopes. Read the review's scope rather than treating an individual source file as an independently deployable assembly. A supplied original source folder for a compiled app is a readable baseline, not verified original source retrieved from Azure.

Function project and Standard Logic App workflow Diff reuse the verified project destination saved for the current Azure account, subscription/resource group and Target environment. Without a mapping, set the returned resource choices in dependency order. A project/workflow name is only a discovery hint; never infer its hosting app from the filename. Read Diff Review's captured destination before handing it to the user. Accounts can forget a local mapping for the next Diff; this does not redirect an existing review or delete an Azure resource.

Check that app components target the intended app solution, Power Pages components target the saved Target-bound Pages solution, and Azure components target the selected Azure resource. Never fix an identity mismatch by retargeting evidence or broadening the selection. Retrieve can overwrite selected local files even though it only reads the remote service; preserve pending work and obtain the required user direction for that local replacement.

An agent may prepare and inspect the review. The user performs deployment, publication, import, deletion, restoration and other external changes through the reviewed action. Report uncertain/incomplete outcomes as such. Never retry a write that may have reached a service, change schema to conceal an error, or declare success from a notification/process exit without the operation's verified outcome.

## Authentication and visible handoff

When a session exposes an authentication prerequisite, call `workbench_session` with `action: "show"` and explain the required provider to the user. They sign in through Workbench's browser flow. After sign-in, read the same session and answer its current `authentication` prompt as follows; this is a continuation acknowledgement, not credential entry:

```json
{
  "sessionId": "<returned sessionId>",
  "surfaceId": "<current authentication surface id>",
  "action": "answer",
  "value": "continue"
}
```

Continue rechecks credentials before starting the original unstarted operation; it does not authorize replaying an uncertain service request. Never enter or disclose credentials, edit token caches, or change the user's ambient CLI account.

To let the user watch a background operation, show the entire session:

```json
{ "sessionId": "<returned sessionId>", "action": "show" }
```

Do not add `surfaceId` or `resultId` to `show` or `release`. Showing retains agent ownership. Once the operation is no longer running, hand visible pages/results to the user:

```json
{ "sessionId": "<returned sessionId>", "action": "release" }
```

Release retires the agent session, preserves visible pages/results and cancels pending confirmations; it does not execute a write. The user reviews and invokes the required action manually. Do not close a released page or continue using its old session IDs. Do not answer a pre-existing user-started one-time prompt on the user's behalf.

If no handoff is needed, close finished agent sessions to release retained resources. An extension-host reload retires sessions; discover fresh context and create a new review afterward. Saved history is evidence and saved selections are drafts, not current credentials or permission to replay actions.

## Report a useful outcome

State the workspace/destination that was actually inspected, the requested scope, the results and any incomplete coverage. Keep secrets and sensitive record data out of logs and public artifacts. Distinguish local preparation, remote read evidence, manual work still required and verified service outcomes. Link the visible retained review or explain the exact next Workbench action rather than claiming a successful operation merely because a tab opened.

For interface changes, treat `agent-catalog.js`, `agent-host.js`, `agent-surfaces.js` and the generated [command access map](COMMAND-ACCESS.md) as the implementation references. Update this guide when their contracts change. The live catalog and returned schemas always determine the tools/actions available in the installed version.
