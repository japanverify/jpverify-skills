# Changelog

## 1.0.2 — 2026-11

- Claude Code plugin: the API key is now a plugin setting (`/plugin` → JPVerify → Configure), stored in the
  system keychain and used only by the MCP server. Plugin skills no longer read `JPVERIFY_API_KEY` from the
  environment; HTTP examples in the plugin show a `<YOUR_JPVERIFY_API_KEY>` placeholder.
- Standalone skills (`skills/`) are unchanged.

## 1.0.1 — 2026-11

- Claude Code plugin: add the plugin icon and `privacyPolicyUrl` (required by the plugin directory for plugins that use a remote MCP server).
- README: privacy policy and terms links.

## 1.0.0 — 2026-11

First release.

- Skills: `jpverify` (router), `jp-invoice-number-check`, `jp-company-verify`, `jp-business-document-ocr`,
  `jp-address`, `jp-supplier-due-diligence`, `jp-pint-invoice-validation`.
- Claude Code plugin `jpverify` with all skills and the JPVerify MCP server (`/plugin marketplace add japanverify/jpverify-skills`).
- Two ways to pay per call: API key with prepaid credits (cards) or x402 (USDC on Base).
