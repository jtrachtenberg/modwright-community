# ModWright release notes

## 0.1.0 (2026-10-04)

The first public release.

- **26 tools** over eight games:
  - install and mod detection, conflicts and load orders;
  - log triage and compatibility checks;
  - the knowledge base and the locally built vanilla indexes;
  - validation, build, deploy, rollback and reload planning;
  - conversion, diff and templates;
  - scaffolds and authoring sequences;
  - the in-game bridge, test plans and the claims ledger.
- **Seven workflow prompts** for any MCP client.
- **First run:** `check_toolchain` reports what works now and what each
  feature needs. ModWright can install Cpp2IL and a UnityPy environment, and
  fetch Stardew Valley's wiki pages, each shown as a plan first.
- **Writers are dry-run by default:** every overwrite and delete is backed up.
