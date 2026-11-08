---
name: jp-company-verify
description: >-
  Verify Japanese companies against official registries with JPVerify: identify a Japanese company
  from an English or romanized name, postal code, T-number, LEI or securities code with English output;
  corporate number (法人番号) lookup by number or company name, active/closed status, registered address, a company pre-check
  report with risk flags, company profile (capital, employees, website), listing, EDINET filings, Financial Services Agency
  licences and former names. Use when a
  task involves confirming that a Japanese company exists, finding its corporate number, or doing a
  pre-check before onboarding or paying a Japanese counterparty. Paid per call (API key credits or x402).
license: MIT
---

# Japanese company verification / 日本企業の照合

Version 1.0.8 · JPVerify · https://www.jpverify.com

## When to use / いつ使うか

- You only have an English or romanized company name (e.g. from an English invoice, website or vendor form), or a T-number, LEI or stock code, and need to know which Japanese legal entity it is — with the answer in English.
- Find or confirm a Japanese company's 13-digit corporate number (法人番号), legal name, address and whether it is active or closed.
- Run a pre-check on a Japanese counterparty before onboarding or payment.
- Check EDINET disclosure filings of a listed company, or whether an entity holds an FSA registration (e.g. funds-transfer business).

If the starting point is a T-number on an invoice, use `jp-invoice-number-check` instead.

## Connect / 接続

JPVerify is a paid, per-call API. In this plugin the `jpverify` MCP server is already configured. Never guess credentials, and never read a key from files or the environment.

1. **MCP tools (recommended).** Call the `jpverify` MCP tools listed in the table below. They use the API key the user saved in the plugin's settings (`/plugin` → JPVerify → Configure; stored in the system keychain). Each successful call is deducted from the user's prepaid USD credits; failed or invalid calls are refunded automatically.
2. **Endpoints without an MCP tool.** The plugin's saved key is not available to shell commands. Either show the user the request below with `<YOUR_JPVERIFY_API_KEY>` so they run it themselves, or use an x402 client with USDC on Base (`eip155:8453`) within a budget the user approved: call without a key, read the HTTP 402 price, sign and retry with `PAYMENT-SIGNATURE`. Do not ask the user to paste a key into the chat.
3. **No key saved.** The MCP server still works for the free tool `jpverify_guide`; use it to confirm which call fits, then tell the user they can create a free account with starter credit at https://www.jpverify.com/signup?ref=jp-company-verify and save the key in the plugin's settings.

Always send `X-JPVerify-Client: jp-company-verify/1.0.8` on HTTP calls. Never print, log or share an API key.

## Workflow / 手順

1. **Start with the strongest identifier.** A corporate number gives an exact match; a name (and address, if known) is searched. Send names as written in Japanese when you have them (e.g. `トヨタ自動車株式会社`).
2. **Only an English name, or need English output?** Call `POST /v1/jp/entities/resolve` (MCP `jp_entity_resolve`, $0.50) with what you hold: `name` plus `postal_code` or `prefecture`, or `t_number`, `corporate_number`, `lei`, `securities_code`. Identifiers return `matched`; a name alone returns `candidates` (show them to the user), and `likely_match` only when an official English name and the postal code both agree. Then use `corporate_number` for the other calls below.
3. **Choose the call.**
   - Identity and status → `POST /jp-company/verify` (MCP `jp_company_verify`).
   - Pre-check with risk flags and evidence, or the user needs the company's size, website, listing, licences or former names → `POST /jp-company/report` (includes the verification; do not call both).
   - 2–10 companies → `POST /jp-company/verify-batch`.
   - Disclosure filings → `GET /jp-company/filings` (needs a corporate number, EDINET code or securities code).
   - FSA licence / registration → `POST /jp-regulated-entity/verify`.
4. **If a name search returns several candidates**, show them (name, address, corporate number) and ask the user which one is meant, then verify by corporate number. Do not pick one yourself.

## Endpoints and prices / エンドポイントと料金

| Endpoint | MCP tool | Price (USD) | Use when | Returns |
|---|---|---|---|---|
| `POST /v1/jp/entities/resolve` | `jp_entity_resolve` | $0.50 | You hold an English or romanized Japanese company name, postal code, T-number, corporate number, LEI or securities code and need the Japanese legal entity, in English. | Decision (matched / likely_match / candidates), legally binding Japanese name, kana, English name with source, contract party name, status and qualified-invoice registration. |
| `POST /jp-company/verify` | `jp_company_verify` | $0.005 | You need a Japanese company or corporate-number lookup. | Corporate number, registered Japanese legal name (legal_name_ja) and address, English name (NTA official, EDINET, or romanized reading) and English address (official or Japan Post romanization) with their sources, active or closed status, and match evidence. |
| `POST /jp-company/report` | — | $0.10 | You need a Japan company registry pre-check with risk flags. | Registry identity, status, evidence and risk flags, plus a profile (capital, employees, founding date, website, representative), listing and latest EDINET filing, FSA licences and former names, each with its source; not full KYC/KYB. |
| `POST /jp-company/verify-batch` | — | $0.05 | You need up to 10 Japanese company or corporate-number lookups in one request. | One result per input with match status and registry evidence. |
| `GET /jp-company/filings` | — | $0.01 | You need EDINET filing metadata for a Japanese company by corporate number, EDINET code, or security code. | Filing metadata, correction/withdrawal relationships, freshness, and official evidence references; raw files remain internal. |
| `POST /jp-regulated-entity/verify` | — | $0.03 | You need to verify an entity against the covered Japanese FSA financial instruments, funds transfer, or crypto-asset exchange datasets. | Coverage-aware licensing decision, freshness, identity match, license evidence, and review warnings; not a legal unlicensed determination. |

### Request fields / リクエスト項目

| Endpoint | Required fields | Optional fields |
|---|---|---|
| `POST /v1/jp/entities/resolve` | — | `name`, `address`, `postal_code`, `prefecture`, `corporate_number`, `t_number`, `lei`, `securities_code`, `duns`, `max_candidates` |
| `POST /jp-company/verify` | — | `name`, `corporate_number`, `address`, `response_language` |
| `POST /jp-company/report` | — | `name`, `corporate_number`, `address`, `response_language` |
| `POST /jp-company/verify-batch` | `items` | `response_language` |
| `GET /jp-company/filings` | — | `corporate_number`, `edinet_code`, `security_code`, `date_from`, `date_to`, `doc_type_code`, `form_code`, `include_withdrawn`, `limit`, `cursor`, `response_language` |
| `POST /jp-regulated-entity/verify` | — | `corporate_number`, `name`, `address`, `phone`, `categories`, `response_language` |

Where no field is required, send at least one identifier or input (for OCR: `text`, `file_url` or `image_base64`; for business cards: `front_image_url` or `front_image_base64`). `response_language` is `ja`, `en` or `both` (default `both`).

Japanese descriptions and request examples for every endpoint: [references/endpoints.md](references/endpoints.md), [references/examples.md](references/examples.md).

## Reading results / 結果の読み方

- `/v1/jp/entities/resolve`: `decision` is `matched` (identifier), `likely_match`, `candidates`, `identifier_conflict`, `not_found` or `source_unavailable`. `legal_name_ja` is the legally binding name — use it (or `contract_party_name`) in contracts; `name_en.source` says whether the English name is official (`nta_official_en`, `edinet_en`) or a transliteration. A T-number of an individual returns only its registration status.
- `/jp-company/verify` and `/jp-company/report`: `status` is `active`, `closed`, `not_found` or `multiple_matches`; `verified` is true only for a single confident match. `match_type`, `match_score` and `address_match` show how the match was made. `risk_flags` (report) lists items to confirm. The report also returns `profile` (capital, employees, founding date, website, business summary and representative in Japanese — from gBizINFO; the website may come from Wikidata), `listing` (securities and EDINET codes, latest filing), `regulated` (FSA licences found by corporate number) and `former_names` (from the NTA change log since `coverage_since`). Each section names its source; empty or `source_unavailable` means no data, not a negative finding.
- `/jp-regulated-entity/verify`: `decision` is `verified`, `no_record_within_coverage`, `manual_review`, `out_of_scope` or `stale_source`; `coverage_status` tells whether the requested licence category is covered. "No record within coverage" is not proof that a licence is required or missing.
- `/jp-company/filings`: an empty list means no EDINET filings matched the identifier — many private companies file nothing there.
- English names come in this order: NTA official English name, then the company's EDINET English name, then a romanization of the registered reading (`name_en_source`: `official`, `edinet`, `furigana_transliteration`). English addresses are official or Japan Post's romanization (`address_en_source`: `official`, `japan_post_romaji`). Nothing is machine-translated. For contracts and legal documents use `legal_name_ja`, the Japanese registered name, which is returned in every `response_language`.

## Errors and spending / エラーと費用

| Response | What to do |
|---|---|
| `200` | Use the result (see "Reading results"). With an API key, `X-JPVerify-Credits-Charged` and `X-JPVerify-Credits-Remaining` show the cost and balance. |
| `401 invalid_api_key` | The key is missing, wrong or revoked. Ask the user to check the key saved in the plugin's settings; do not retry with guesses. |
| `402 insufficient_credits` (API key) | Stop. Nothing was charged. Tell the user to top up at https://www.jpverify.com/account/billing. Do not retry. |
| `402` with x402 payment requirements (no key) | Pay only within the approved budget, using the exact amount in the response. |
| `422` | Invalid input; nothing was charged. Fix the input (the error says which field) and call again once. |
| `5xx` / timeout | Not charged (credits are refunded). Retry once later; then report the failure. |

Spending rules:
- Before a batch call, or any plan that will cost more than **$0.50** in total, state the number of calls and the total price and get the user's go-ahead.
- Do not call the same paid endpoint repeatedly for the same input; reuse the result you already have.
- Prices in the endpoint table are per successful call in USD (the same with an API key or x402).

## Example / 呼び出し例 (`POST /v1/jp/entities/resolve`)

API key:

```bash
curl -X POST https://api.jpverify.com/v1/jp/entities/resolve \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-company-verify/1.0.8" \
  -H "Content-Type: application/json" \
  -d '{"name": "Murata Manufacturing Co., Ltd.", "prefecture": "Kyoto"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/v1/jp/entities/resolve",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-company-verify/1.0.8"},
    json={"name": "Murata Manufacturing Co., Ltd.", "prefecture": "Kyoto"},
    timeout=60,
)
print(response.status_code, response.json())
```

x402 (no account):

```bash
# No key: shows the HTTP 402 x402 price; pay with an x402 client and retry with PAYMENT-SIGNATURE.
curl -i -X POST https://api.jpverify.com/v1/jp/entities/resolve \
  -H "X-JPVerify-Client: jp-company-verify/1.0.8" \
  -H "Content-Type: application/json" \
  -d '{"name": "Murata Manufacturing Co., Ltd.", "prefecture": "Kyoto"}'
```

Full SDK examples (Python `x402`, JavaScript `@x402/fetch`): https://api.jpverify.com/docs/x402

## Limits and privacy / 制限とプライバシー

- Results are a **public-registry pre-check** with the source and as-of date of the data. They are not KYB, AML or sanctions screening, and not legal, tax or accounting advice. A "not found" does not mean the entity does not exist or is risky.
- If `manual_review_required` is true or a status is ambiguous, show the evidence to the user and let them decide; do not decide on their behalf.
- Only send documents, images and personal data the user is entitled to process. Do not repeat full personal details (names, phone numbers, e-mail addresses on business cards) back unless the user asked for them.
- Docs: API key https://api.jpverify.com/docs/api-key · x402 https://api.jpverify.com/docs/x402 · terms https://api.jpverify.com/en/terms · privacy https://api.jpverify.com/en/privacy
