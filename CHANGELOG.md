# Changelog

## 1.3.0 — 2026-11

Better results from the same endpoints; no field was removed or renamed, prices are unchanged.

- **Company lookup (`/v1/jp/companies/resolve`)**: common readings no longer hide the company you mean
  (`Murata` → 村田製作所, `Sagawa` → 佐川急便); brand names find the owning company as a candidate (`au` → KDDI,
  `MOS Burger` → モスフードサービス, `Pocari Sweat` → 大塚製薬). Listing, Wikidata prominence and gBizINFO employee
  counts order candidates (new `signals` `wikidata_sitelinks`, `company_size`); they never confirm a match. A
  business-specific postal code counts as postal evidence when its office name matches. Website-only lookups also
  use gBizINFO websites. `sources` lists gBizINFO when it was used.
- **Postal codes and addresses**: Japan Post business-specific postal codes (事業所個別郵便番号, about 22,000) are
  supported. `/v1/jp/postal-codes/lookup` matches have `postal_code_type` (`area` / `business_specific`) and, for a
  business code, `business_name`, `street` and `business_kind`; `normalize` and `validate` accept them. Parcel
  addresses (地番) now match at level 7 instead of `not_found`; Hokkaido grid towns (宮の森1条 / 宮の森一条) match; one
  house stored under two spellings is no longer `ambiguous` (warning `duplicate_spelling_candidates_merged`).
- **Company report**: when the live gBizINFO source is unavailable, `profile` comes from a dated gBizINFO snapshot
  (`retrieval: bulk_snapshot`, no representative name).

## 1.2.1 — 2026-11

- New free `GET /v1/account/balance` for API-key users: returns `balance_usd`, `free_credit_usd`, `paid_credit_usd` and
  `top_up_url`; nothing is charged. Over MCP, the free tool `jpverify_balance` returns the same. Check it before a large
  batch.

## 1.2.0 — 2026-11

- **New endpoint names** (`/v1/jp/{resource}/{action}`). Same prices, same request and response fields. The old paths
  keep working until 2027-02-10 and answer with `Deprecation`, `Sunset` and `Link: rel="successor-version"` headers:

  | Old | New |
  |---|---|
  | `/jp-company/verify`, `/jp-company/verify-batch`, `/jp-company/report` | `/v1/jp/companies/verify`, `/v1/jp/companies/verify-batch`, `/v1/jp/companies/report` |
  | `/v1/jp/entities/resolve` (MCP `jp_entity_resolve`) | `/v1/jp/companies/resolve` (MCP `jp_company_resolve`) |
  | `/v1/suppliers/resolve` | `/v1/jp/suppliers/resolve` |
  | `/jp-regulated-entity/verify` | `/v1/jp/regulated-entities/verify` |
  | `GET /jp-company/filings` | `GET /v1/jp/filings` |
  | `/jp-invoice/verify`, `/jp-invoice/verify-batch` | `/v1/jp/invoice-registrations/verify`, `/v1/jp/invoice-registrations/verify-batch` |
  | `/jp-invoice/check` | `/v1/jp/invoices/check-issuer` |
  | `/v1/invoices/validate-jp-pint` | `/v1/jp/invoices/validate-pint` |
  | `/jp-address/normalize`, `parse`, `geocode`, `validate` | `/v1/jp/addresses/normalize`, `parse`, `geocode`, `validate` |
  | `/jp-address/postal-lookup` | `/v1/jp/postal-codes/lookup` |

- Error codes are lower-case `snake_case` everywhere (`endpoint_retired`, `query_required`, `identity_required`, ...).
- **Synchronous batches take up to 100 items and are priced per item** (was 10 items for a fixed $0.08):
  `/v1/jp/companies/verify-batch` and `/v1/jp/invoice-registrations/verify-batch` cost $0.008 per item, so a batch is
  never dearer than single calls. They now work with an API key as well as x402.
- New `POST /v1/jp/invoices/check-issuer-batch`: the invoice issuer check on up to 100 invoices in one call, $0.04 per
  invoice (single check $0.05). `items` take the same fields as `/v1/jp/invoices/check-issuer`.
- `/v1/batch-jobs` operations `invoice_verify` and `invoice_check` are renamed `invoice_registration_verify` and
  `invoice_issuer_check` (old names accepted until 2027-02-10). An unpaid job is held for 30 minutes (was 7 days);
  paid jobs and their results are still kept for 7 days.

## 1.1.0 — 2026-11

- **Breaking: document OCR is retired.** JPVerify no longer reads files. `/jp-invoice/extract`, `/jp-receipt/extract`,
  `/jp-business-card/extract`, `/jp-business-card/extract-and-enrich`, `/jp-invoice/extract-and-verify` and
  `/v1/invoices/issuer-match/extract` (and their MCP tools) answer HTTP 410 with the replacement, free of charge.
  The `jp-business-document-ocr` skill is removed.
- `/jp-invoice/check` adds an optional `invoice_date`: it checks that the issuer was registered, and not cancelled or
  expired, on that date (`invoice_date_outside_registration_period` otherwise). `check_results` gives one status per
  check: T-number, issuer name, address, registration on the invoice date. Price $0.05 (was $0.03).
- `/jp-invoice/check` adds optional `expected_registration_number` / `expected_corporate_number` from your supplier
  master data. `/v1/invoices/issuer-match` (MCP `jp_invoice_issuer_match`) is merged into it and answers HTTP 410.
- From a business card, use the English company lookup (name, address, website, e-mail) or `/jp-company/verify`.

## 1.0.10 — 2026-11

- English addresses report `completeness` (`complete` / `partial` / `unavailable`) separately from confidence. With
  `response_language=en`, a partial English address no longer replaces `address`; the Japanese original stays. Supplier
  checks (`address_en_completeness`) and the English company lookup (`address.en_completeness`) carry the same signal.
  Town names with a space in Japan Post data (上鳥羽 鉾立町) now romanize; numeric floors and rooms render as `Floor 25`,
  `Room 806`.
- `/jp-company/verify`, `/jp-company/report` and `/v1/suppliers/resolve` search registered Japanese names only. An
  English or romanized name that is not found now says so: risk flag / reason `english_name_not_in_registry_index`
  plus `next_step` pointing to `/v1/jp/entities/resolve`.
- Romanized company names keep long vowels (`Yunikoon Paatonaazu`, not `Yuniko Npa Tona Zu`).
- Invoice OCR: printed issuer labels (`発行者:`) are removed from `supplier_name` and from issuer-match inputs, which
  removes false `document_conflict` results; date lines are no longer read as line items.

## 1.0.9 — 2026-11

- New prices: `/v1/jp/entities/resolve` $0.25 (was $0.50), `/jp-invoice/extract-and-verify` $0.10, `/jp-invoice/extract`
  $0.03, `/jp-company/verify` $0.01, company and invoice batch verification $0.08 per batch of up to 10.
- English company lookup also takes an English or Japanese `address` (municipality and town are compared), a `website`
  or company `email` domain (a website alone can find the company), and resolves an LEI offline from GLEIF data; GLEIF
  English legal names are a new English-name source (`gleif`).
- `decision_summary`: one decision vocabulary (`verified`, `likely_match`, `candidates`, `not_found`, `manual_review`,
  `insufficient_coverage`) across company, invoice, supplier, entity, issuer-match and address results.
- `jp-address`: `normalized_en_common` with the usual spelling (Marunouchi).

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
