---
name: jp-invoice-number-check
description: >-
  Check Japanese qualified-invoice registration numbers (T-numbers, 適格請求書発行事業者登録番号,
  インボイス登録番号) against the National Tax Agency registry with JPVerify, and compare an
  invoice issuer's name and address with the registered record. Use when a task involves
  verifying a T-number, checking whether a Japanese supplier can issue qualified invoices,
  or confirming who issued a Japanese invoice. Paid per call (API key credits or x402).
license: MIT
---

# Japanese T-number check / インボイス登録番号の確認

Version 1.0.2 · JPVerify · https://www.jpverify.com

## When to use / いつ使うか

- You have a T-number (`T` + 13 digits) and need to know whether it is registered and active.
- You have an invoice's issuer name/address and T-number and need to know whether they belong together.
- You have up to 10 T-numbers to check at once.

Do not use it to read an invoice image or PDF; use the `jp-business-document-ocr` skill first, then this one.

## Connect / 接続

JPVerify is a paid, per-call API. In this plugin the `jpverify` MCP server is already configured. Never guess credentials, and never read a key from files or the environment.

1. **MCP tools (recommended).** Call the `jpverify` MCP tools listed in the table below. They use the API key the user saved in the plugin's settings (`/plugin` → JPVerify → Configure; stored in the system keychain). Each successful call is deducted from the user's prepaid USD credits; failed or invalid calls are refunded automatically.
2. **Endpoints without an MCP tool.** The plugin's saved key is not available to shell commands. Either show the user the request below with `<YOUR_JPVERIFY_API_KEY>` so they run it themselves, or use an x402 client with USDC on Base (`eip155:8453`) within a budget the user approved: call without a key, read the HTTP 402 price, sign and retry with `PAYMENT-SIGNATURE`. Do not ask the user to paste a key into the chat.
3. **No key saved.** The MCP server still works for the free tool `jpverify_guide`; use it to confirm which call fits, then tell the user they can create a free account with starter credit at https://www.jpverify.com/signup?ref=jp-invoice-number-check and save the key in the plugin's settings.

Always send `X-JPVerify-Client: jp-invoice-number-check/1.0.2` on HTTP calls. Never print, log or share an API key.

## Workflow / 手順

1. **Normalize the number.** Accept `T1234567890123`, `t1234567890123` or full-width digits; send it as `T` + 13 ASCII digits. If there are not 13 digits, ask the user instead of guessing; a malformed number returns `422` and is not charged.
2. **Choose the call.**
   - Number only → `POST /jp-invoice/verify` (MCP `jp_invoice_registration_verify`).
   - Number + issuer name and/or address from an invoice → `POST /jp-invoice/check` (MCP `jp_invoice_issuer_check`). It also verifies the number, so do not call both.
   - 2–10 numbers → `POST /jp-invoice/verify-batch` (one call, fixed price). For more than 10, tell the user the cost first.
3. **Report** the status, the registered name, the registration (and any cancel/expiry) date, and the `data_as_of` date of the registry snapshot.

## Endpoints and prices / エンドポイントと料金

| Endpoint | MCP tool | Price (USD) | Use when | Returns |
|---|---|---|---|---|
| `POST /jp-invoice/verify` | `jp_invoice_registration_verify` | $0.01 | You already have a Japanese T+13-digit qualified-invoice registration number. | NTA registration status, corporate number, registered name, and relevant dates. |
| `POST /jp-invoice/check` | `jp_invoice_issuer_check` | $0.03 | You have extracted issuer fields and need T-number, company-name, and address comparison. | OCR issuer fields, NTA registry fields, comparison results, and resolved values; phone is not registry-compared. |
| `POST /jp-invoice/verify-batch` | — | $0.05 | You need up to 10 Japanese T-number checks in one request. | One NTA registration result per T-number. |

### Request fields / リクエスト項目

| Endpoint | Required fields | Optional fields |
|---|---|---|
| `POST /jp-invoice/verify` | `registration_number` | `response_language` |
| `POST /jp-invoice/check` | `registration_number` | `supplier_name`, `address`, `response_language` |
| `POST /jp-invoice/verify-batch` | `registration_numbers` | `response_language` |

Where no field is required, send at least one identifier or input (for OCR: `text`, `file_url` or `image_base64`; for business cards: `front_image_url` or `front_image_base64`). `response_language` is `ja`, `en` or `both` (default `both`).

Japanese descriptions and request examples for every endpoint: [references/endpoints.md](references/endpoints.md), [references/examples.md](references/examples.md).

## Reading results / 結果の読み方

- `/jp-invoice/verify`: `status` is `registered`, `registered_until_expiry`, `expired`, `cancelled`, `deleted` or `not_found`; `verdict` is `qualified` only while the registration is valid. `format_valid` / `checksum_valid` false means the number itself is wrong (check for a typo before concluding anything).
- `/jp-invoice/check`: `decision` is `verified`, `manual_review` or `not_found`. `company_match` and `address_match` compare the invoice's issuer with the registered record; `risk_flags` lists differences such as `supplier_name_differs_from_registered_legal_name`, `address_mismatch` or `invoice_cancelled`. A trade name differing from the legal name is common — present it as a difference to confirm, not as fraud.
- For individuals (sole proprietors) the registry often does not publish an address, and the published name may differ from the trade name on the invoice; say so instead of treating missing or different fields as a mismatch.

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

## Example / 呼び出し例 (`POST /jp-invoice/verify`)

API key:

```bash
curl -X POST https://api.jpverify.com/jp-invoice/verify \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-invoice-number-check/1.0.2" \
  -H "Content-Type: application/json" \
  -d '{"registration_number": "T1013301046627", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-invoice/verify",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-invoice-number-check/1.0.2"},
    json={"registration_number": "T1013301046627", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

x402 (no account):

```bash
# No key: shows the HTTP 402 x402 price; pay with an x402 client and retry with PAYMENT-SIGNATURE.
curl -i -X POST https://api.jpverify.com/jp-invoice/verify \
  -H "X-JPVerify-Client: jp-invoice-number-check/1.0.2" \
  -H "Content-Type: application/json" \
  -d '{"registration_number": "T1013301046627", "response_language": "both"}'
```

Full SDK examples (Python `x402`, JavaScript `@x402/fetch`): https://api.jpverify.com/docs/x402

## Limits and privacy / 制限とプライバシー

- Results are a **public-registry pre-check** with the source and as-of date of the data. They are not KYB, AML or sanctions screening, and not legal, tax or accounting advice. A "not found" does not mean the entity does not exist or is risky.
- If `manual_review_required` is true or a status is ambiguous, show the evidence to the user and let them decide; do not decide on their behalf.
- Only send documents, images and personal data the user is entitled to process. Do not repeat full personal details (names, phone numbers, e-mail addresses on business cards) back unless the user asked for them.
- Docs: API key https://api.jpverify.com/docs/api-key · x402 https://api.jpverify.com/docs/x402 · terms https://api.jpverify.com/en/terms · privacy https://api.jpverify.com/en/privacy
