# Executable query examples

`query-corpus.json` supplies portable examples for Query Data's URL and JSON editors. Select Account in a solution that includes it and substitute actual readable columns when your schema differs. The companion `query-corpus.test.js` runs every example against synthetic metadata without contacting an environment.

The corpus covers ordinary columns/filters/order, URL encoding, date/numeric conditions, summaries and nested related results. The existing `query-*.test.js`, `unified-query*.test.js` and Query Data integration/browser suites cover the larger language, calculation and visual-control contracts. Run the complete corpus with `Test-LocalSelection.ps1`.

URL may carry encoded native FetchXML. Nested result projections can require coordinated native queries; JSON retains the complete plan, while URL/XML show the selected native piece. Execute that complete plan through Workbench. One piece alone does not reproduce nested results or Workbench calculations. Every XML piece remains native FetchXML; this is not a claim that different native query languages have identical expressive power.
