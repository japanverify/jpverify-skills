# jp-invoice-number-check — endpoints

Generated from the live JPVerify catalog. Prices are USD per successful call (the same with an API key or x402).

## English

| Endpoint | MCP tool | Price (USD) | Use when | Returns |
|---|---|---|---|---|
| `POST /jp-invoice/verify` | `jp_invoice_registration_verify` | $0.01 | You already have a Japanese T+13-digit qualified-invoice registration number. | NTA registration status, corporate number, registered name, and relevant dates. |
| `POST /jp-invoice/check` | `jp_invoice_issuer_check` | $0.05 | You have a T-number and the issuer name from an invoice (address and invoice date optional) and need to know whether they belong together and the issuer was registered on the invoice date. | Registry fields, issuer name and address comparison, registration on the invoice date, supplier master comparison, and one status per check in check_results. |
| `POST /jp-invoice/verify-batch` | — | $0.08 | You need up to 10 Japanese T-number checks in one request. | One NTA registration result per T-number. |

## 日本語

| Endpoint | MCP tool | Price (USD) | Use when | Returns |
|---|---|---|---|---|
| `POST /jp-invoice/verify` | `jp_invoice_registration_verify` | $0.01 | 日本のT+13桁インボイス登録番号が既にある場合。 | 国税庁の登録状態、法人番号、登録名、関連日付を返します。 |
| `POST /jp-invoice/check` | `jp_invoice_issuer_check` | $0.05 | 請求書のT番号と発行者名（住所・日付は任意）が一致するか、請求書の日付時点で登録済みかを確認したい場合。 | 国税庁の登録情報、発行者名・住所の比較結果、請求書日付時点の登録状況、確認項目ごとの結果を返します。 |
| `POST /jp-invoice/verify-batch` | — | $0.08 | 最大10件の日本のT番号を1回のリクエストで検証したい場合。 | T番号ごとの国税庁登録結果を返します。 |
