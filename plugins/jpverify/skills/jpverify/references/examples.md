# jpverify — request examples

Replace `<YOUR_JPVERIFY_API_KEY>` with your key (create one at https://www.jpverify.com/signup?ref=jpverify) and run the command yourself; inside Claude Code prefer the plugin's MCP tools. Without a key the API answers HTTP 402 with the x402 price; see https://api.jpverify.com/docs/x402 for x402 SDK code.

## `POST /v1/jp/entities/resolve` — $0.25

```bash
curl -X POST https://api.jpverify.com/v1/jp/entities/resolve \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"name": "Murata Manufacturing Co., Ltd.", "prefecture": "Kyoto"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/v1/jp/entities/resolve",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"name": "Murata Manufacturing Co., Ltd.", "prefecture": "Kyoto"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-invoice/verify` — $0.01

```bash
curl -X POST https://api.jpverify.com/jp-invoice/verify \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"registration_number": "T1013301046627", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-invoice/verify",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"registration_number": "T1013301046627", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-invoice/check` — $0.05

```bash
curl -X POST https://api.jpverify.com/jp-invoice/check \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"registration_number": "T1013301046627", "supplier_name": "株式会社ABC", "address": "東京都千代田区千代田1-1", "invoice_date": "2026-11-01", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-invoice/check",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"registration_number": "T1013301046627", "supplier_name": "株式会社ABC", "address": "東京都千代田区千代田1-1", "invoice_date": "2026-11-01", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-invoice/verify-batch` — $0.08

```bash
curl -X POST https://api.jpverify.com/jp-invoice/verify-batch \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"registration_numbers": ["T1013301046627"], "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-invoice/verify-batch",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"registration_numbers": ["T1013301046627"], "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-company/verify` — $0.01

```bash
curl -X POST https://api.jpverify.com/jp-company/verify \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"name": "トヨタ自動車株式会社", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-company/verify",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"name": "トヨタ自動車株式会社", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-company/report` — $0.10

```bash
curl -X POST https://api.jpverify.com/jp-company/report \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"name": "株式会社ABC", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-company/report",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"name": "株式会社ABC", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-company/verify-batch` — $0.08

```bash
curl -X POST https://api.jpverify.com/jp-company/verify-batch \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"items": [{"corporate_number": "1180301018771"}], "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-company/verify-batch",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"items": [{"corporate_number": "1180301018771"}], "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /v1/suppliers/resolve` — $0.10

```bash
curl -X POST https://api.jpverify.com/v1/suppliers/resolve \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"name": "株式会社ABC", "registration_number": "T1013301046627", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/v1/suppliers/resolve",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"name": "株式会社ABC", "registration_number": "T1013301046627", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-regulated-entity/verify` — $0.03

```bash
curl -X POST https://api.jpverify.com/jp-regulated-entity/verify \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"corporate_number": "1234567890123", "categories": ["funds_transfer_business"], "response_language": "en"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-regulated-entity/verify",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"corporate_number": "1234567890123", "categories": ["funds_transfer_business"], "response_language": "en"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `GET /jp-company/filings` — $0.01

```bash
curl -G https://api.jpverify.com/jp-company/filings \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  --data-urlencode 'edinet_code=E00000' \
  --data-urlencode 'limit=20' \
  --data-urlencode 'response_language=en'
```

```python
import requests

response = requests.get(
    "https://api.jpverify.com/jp-company/filings",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    params={"edinet_code": "E00000", "limit": 20, "response_language": "en"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-address/normalize` — $0.005

```bash
curl -X POST https://api.jpverify.com/jp-address/normalize \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"address": "東京都新宿区西新宿2丁目8番1号", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-address/normalize",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"address": "東京都新宿区西新宿2丁目8番1号", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-address/parse` — $0.005

```bash
curl -X POST https://api.jpverify.com/jp-address/parse \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"address": "東京都千代田区千代田1丁目1番1号", "response_language": "ja"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-address/parse",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"address": "東京都千代田区千代田1丁目1番1号", "response_language": "ja"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-address/postal-lookup` — $0.001

```bash
curl -X POST https://api.jpverify.com/jp-address/postal-lookup \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"postal_code": "100-0001", "max_results": 10}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-address/postal-lookup",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"postal_code": "100-0001", "max_results": 10},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-address/geocode` — $0.005

```bash
curl -X POST https://api.jpverify.com/jp-address/geocode \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"address": "東京都千代田区千代田1丁目1番1号", "max_candidates": 3}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-address/geocode",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"address": "東京都千代田区千代田1丁目1番1号", "max_candidates": 3},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-address/validate` — $0.005

```bash
curl -X POST https://api.jpverify.com/jp-address/validate \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Content-Type: application/json" \
  -d '{"address": "東京都千代田区千代田1丁目1番1号", "required_level": 8, "strict": false}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-address/validate",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"address": "東京都千代田区千代田1丁目1番1号", "required_level": 8, "strict": False},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /v1/invoices/validate-jp-pint` — $0.02

```bash
# invoice.xml: your JP PINT 1.1.3 UBL Invoice
jq -n --rawfile xml invoice.xml '{xml: $xml, registry_enhance: true}' |
  curl -X POST https://api.jpverify.com/v1/invoices/validate-jp-pint \
    -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
    -H "X-JPVerify-Client: jpverify/1.1.0" \
    -H "Content-Type: application/json" \
    --data-binary @-
```

```python
import pathlib, requests

response = requests.post(
    "https://api.jpverify.com/v1/invoices/validate-jp-pint",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jpverify/1.1.0"},
    json={"xml": pathlib.Path("invoice.xml").read_text(encoding="utf-8"), "registry_enhance": True},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /v1/batch-jobs` — per item (x402 only)

x402 only (not available with an API key). Choose your own capability token (32+ characters) and a unique `Idempotency-Key` per job.

```bash
# No key: shows the HTTP 402 x402 price; pay with an x402 client and retry with PAYMENT-SIGNATURE.
curl -i -X POST https://api.jpverify.com/v1/batch-jobs \
  -H "X-JPVerify-Client: jpverify/1.1.0" \
  -H "Authorization: Bearer REPLACE_WITH_YOUR_OWN_RANDOM_TOKEN_32_CHARS_OR_MORE" \
  -H "Idempotency-Key: my-batch-job-0001" \
  -H "Content-Type: application/json" \
  -d '{"operation": "invoice_verify", "items": [{"registration_number": "T1013301046627"}, {"registration_number": "T1180301018771"}], "response_language": "en"}'
```
