# Changelog

## 1.0.4 — 2026-11

- `jp-invoice-number-check` and the `jpverify` router: new Japan Invoice Issuer Match endpoints
  `POST /v1/invoices/issuer-match` ($0.05) and `/extract` ($0.10) — match an invoice issuer to the supplier's expected
  T-number or corporate number from master data, with invoice, expected and registry values kept separate.

## 1.0.3 — 2026-11

- Standalone skills: the Connect section now says the Claude Code plugin uses the key saved in the plugin's
  settings (since 1.0.2), not `JPVERIFY_API_KEY`.

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
