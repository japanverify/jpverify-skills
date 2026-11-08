# Changelog

## 1.0.8 — 2026-11

- English company lookup now finds brand, short and former names (Sony, Uniqlo, MUJI, Totsuko) from Wikidata; unions
  and associations no longer outrank companies with the same reading.
- `/jp-company/report` ($0.10) adds `profile` (capital, employees, website, representative), `listing` (securities code,
  latest EDINET filing), `regulated` (FSA licences) and `former_names`. `/v1/suppliers/resolve` is now $0.10.
- `/jp-invoice/extract-and-verify` and `/v1/invoices/issuer-match/extract` return `invoice_eligibility` for input tax
  credit (eligible / not_eligible / needs_review).
- Retired: UK Companies House, EU VIES / Peppol and SEC EDGAR checks (`/v1/entities/resolve-cross-border`,
  `/v1/invoices/counterparty-check`, `/v1/entities/{cik}/filing-risk-snapshot`, MCP `jp_counterparty_check`).
  JPVerify now covers Japan only.

## 1.0.7 — 2026-11

- English output is now filled in across the API, and the skills say where it comes from. English company names: NTA
  official, then the company's EDINET English name, then a romanized reading. English addresses: official, or Japan Post's
  romanization (`*_source: japan_post_romaji`). `legal_name_ja` (the Japanese registered name) is always returned and is
  the name to use on legal documents. `jp-address`: `/jp-address/parse` returns `normalized_en` and `components_en`, and
  postal-lookup matches return `prefecture_en`, `city_en`, `town_en`.

## 1.0.6 — 2026-11

- New English entity resolve endpoint `POST /v1/jp/entities/resolve` ($0.50, MCP `jp_entity_resolve`), listed first in
  `jp-company-verify`, `jp-supplier-due-diligence` and the `jpverify` router: identify a Japanese company from an English or
  romanized name with postal code or prefecture, or from a T-number, corporate number, LEI or securities code, with
  English output and the legally binding Japanese name.

## 1.0.5 — 2026-11

- Issuer-match examples now use a real public NTA record (T1013301046627) instead of a placeholder number.

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
