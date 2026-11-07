---
name: jp-pint-invoice-validation
description: >-
  Validate Japanese Peppol e-invoices (JP PINT 1.1.3 UBL Invoice XML) with JPVerify against the
  official OASIS UBL 2.1 XSD and OpenPeppol JP PINT Schematron, optionally checking the seller's
  qualified-invoice T-number in the National Tax Agency registry. Use when a task involves checking a
  JP PINT / Peppol (デジタルインボイス) XML invoice before sending it, accepting it into accounting, or
  paying it. Paid per call (API key credits or x402).
license: MIT
---

# JP PINT e-invoice validation / JP PINT デジタルインボイスの検証

Version 1.0.2 · JPVerify · https://www.jpverify.com

## When to use / いつ使うか

- You have a JP PINT 1.1.3 UBL **Invoice** XML (Japanese Peppol e-invoice / デジタルインボイス) and need to know whether it is valid.
- You are building or testing e-invoice generation and want rule-level errors to fix.
- You received an e-invoice and want schema/rule validity plus the seller's T-number status before accepting it.

Not for PDF or image invoices — use `jp-business-document-ocr` for those.

## Connect / 接続

JPVerify is a paid, per-call API. In this plugin the `jpverify` MCP server is already configured. Never guess credentials, and never read a key from files or the environment.

1. **MCP tools (recommended).** Call the `jpverify` MCP tools listed in the table below. They use the API key the user saved in the plugin's settings (`/plugin` → JPVerify → Configure; stored in the system keychain). Each successful call is deducted from the user's prepaid USD credits; failed or invalid calls are refunded automatically.
2. **Endpoints without an MCP tool.** The plugin's saved key is not available to shell commands. Either show the user the request below with `<YOUR_JPVERIFY_API_KEY>` so they run it themselves, or use an x402 client with USDC on Base (`eip155:8453`) within a budget the user approved: call without a key, read the HTTP 402 price, sign and retry with `PAYMENT-SIGNATURE`. Do not ask the user to paste a key into the chat.
3. **No key saved.** The MCP server still works for the free tool `jpverify_guide`; use it to confirm which call fits, then tell the user they can create a free account with starter credit at https://www.jpverify.com/signup?ref=jp-pint-invoice-validation and save the key in the plugin's settings.

Always send `X-JPVerify-Client: jp-pint-invoice-validation/1.0.2` on HTTP calls. Never print, log or share an API key.

## Workflow / 手順

1. Send the complete XML document as the `xml` string (UTF-8, starting with the XML declaration or `<Invoice`). Do not pretty-print or edit it; validation is on the exact document.
2. Set `registry_enhance: true` if you also need the seller's T-number checked in the NTA registry (same price).
3. If `document_valid` is false, list the failing rules from `errors` / `checks` (rule ID, message, location) so the user or developer can fix them, then validate the corrected file once.

## Endpoints and prices / エンドポイントと料金

| Endpoint | MCP tool | Price (USD) | Use when | Returns |
|---|---|---|---|---|
| `POST /v1/invoices/validate-jp-pint` | — | $0.02 | You need offline validation of a JP PINT 1.1.3 UBL Invoice before accounting or payment workflow acceptance. | Official document_valid from pinned OASIS UBL 2.1 XSD and OpenPeppol JP PINT 1.1.3 Schematron, plus separate decision/trusted fields and optional NTA registry evidence. |

### Request fields / リクエスト項目

| Endpoint | Required fields | Optional fields |
|---|---|---|
| `POST /v1/invoices/validate-jp-pint` | `xml` | `registry_enhance` |

Where no field is required, send at least one identifier or input (for OCR: `text`, `file_url` or `image_base64`; for business cards: `front_image_url` or `front_image_base64`). `response_language` is `ja`, `en` or `both` (default `both`).

Japanese descriptions and request examples for every endpoint: [references/endpoints.md](references/endpoints.md), [references/examples.md](references/examples.md).

## Reading results / 結果の読み方

- `document_valid` is the official result: the document passed the pinned UBL 2.1 XSD and the JP PINT 1.1.3 Schematron (`version` shows what was applied).
- `decision` (`accepted`, `rejected`, `manual_review`, `not_evaluated`) and `trusted` are a separate workflow signal that also considers registry evidence when `registry_enhance` is on. A document can be schema-valid but still need review (e.g. the T-number is not registered).
- `warnings` are non-fatal rule warnings; report them but do not treat them as failures.
- The result is validation evidence only — not legal, tax or Peppol conformance certification.

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

## Example / 呼び出し例 (`POST /v1/invoices/validate-jp-pint`)

API key:

```bash
# invoice.xml: your JP PINT 1.1.3 UBL Invoice
jq -n --rawfile xml invoice.xml '{xml: $xml, registry_enhance: true}' |
  curl -X POST https://api.jpverify.com/v1/invoices/validate-jp-pint \
    -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
    -H "X-JPVerify-Client: jp-pint-invoice-validation/1.0.2" \
    -H "Content-Type: application/json" \
    --data-binary @-
```

```python
import pathlib, requests

response = requests.post(
    "https://api.jpverify.com/v1/invoices/validate-jp-pint",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-pint-invoice-validation/1.0.2"},
    json={"xml": pathlib.Path("invoice.xml").read_text(encoding="utf-8"), "registry_enhance": True},
    timeout=60,
)
print(response.status_code, response.json())
```

x402 (no account):

```bash
# No key: shows the HTTP 402 x402 price; pay with an x402 client and retry with PAYMENT-SIGNATURE.
jq -n --rawfile xml invoice.xml '{xml: $xml, registry_enhance: true}' |
  curl -i -X POST https://api.jpverify.com/v1/invoices/validate-jp-pint \
    -H "X-JPVerify-Client: jp-pint-invoice-validation/1.0.2" \
    -H "Content-Type: application/json" \
    --data-binary @-
```

Full SDK examples (Python `x402`, JavaScript `@x402/fetch`): https://api.jpverify.com/docs/x402

## Limits and privacy / 制限とプライバシー

- Results are a **public-registry pre-check** with the source and as-of date of the data. They are not KYB, AML or sanctions screening, and not legal, tax or accounting advice. A "not found" does not mean the entity does not exist or is risky.
- If `manual_review_required` is true or a status is ambiguous, show the evidence to the user and let them decide; do not decide on their behalf.
- Only send documents, images and personal data the user is entitled to process. Do not repeat full personal details (names, phone numbers, e-mail addresses on business cards) back unless the user asked for them.
- Docs: API key https://api.jpverify.com/docs/api-key · x402 https://api.jpverify.com/docs/x402 · terms https://api.jpverify.com/en/terms · privacy https://api.jpverify.com/en/privacy
