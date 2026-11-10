# jp-company-verify — request examples

Replace `<YOUR_JPVERIFY_API_KEY>` with your key (create one at https://www.jpverify.com/signup?ref=jp-company-verify) and run the command yourself; inside Claude Code prefer the plugin's MCP tools. Without a key the API answers HTTP 402 with the x402 price; see https://api.jpverify.com/docs/x402 for x402 SDK code.

## `POST /v1/jp/companies/resolve` — $0.25

```bash
curl -X POST https://api.jpverify.com/v1/jp/companies/resolve \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-company-verify/1.3.0" \
  -H "Content-Type: application/json" \
  -d '{"name": "Murata Manufacturing Co., Ltd.", "prefecture": "Kyoto"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/v1/jp/companies/resolve",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-company-verify/1.3.0"},
    json={"name": "Murata Manufacturing Co., Ltd.", "prefecture": "Kyoto"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /v1/jp/companies/verify` — $0.01

```bash
curl -X POST https://api.jpverify.com/v1/jp/companies/verify \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-company-verify/1.3.0" \
  -H "Content-Type: application/json" \
  -d '{"name": "トヨタ自動車株式会社", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/v1/jp/companies/verify",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-company-verify/1.3.0"},
    json={"name": "トヨタ自動車株式会社", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /v1/jp/companies/report` — $0.10

```bash
curl -X POST https://api.jpverify.com/v1/jp/companies/report \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-company-verify/1.3.0" \
  -H "Content-Type: application/json" \
  -d '{"name": "株式会社ABC", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/v1/jp/companies/report",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-company-verify/1.3.0"},
    json={"name": "株式会社ABC", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /v1/jp/companies/verify-batch` — $0.008 per item

```bash
curl -X POST https://api.jpverify.com/v1/jp/companies/verify-batch \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-company-verify/1.3.0" \
  -H "Content-Type: application/json" \
  -d '{"items": [{"corporate_number": "1180301018771"}], "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/v1/jp/companies/verify-batch",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-company-verify/1.3.0"},
    json={"items": [{"corporate_number": "1180301018771"}], "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `GET /v1/jp/filings` — $0.01

```bash
curl -G https://api.jpverify.com/v1/jp/filings \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-company-verify/1.3.0" \
  --data-urlencode 'edinet_code=E00000' \
  --data-urlencode 'limit=20' \
  --data-urlencode 'response_language=en'
```

```python
import requests

response = requests.get(
    "https://api.jpverify.com/v1/jp/filings",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-company-verify/1.3.0"},
    params={"edinet_code": "E00000", "limit": 20, "response_language": "en"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /v1/jp/regulated-entities/verify` — $0.03

```bash
curl -X POST https://api.jpverify.com/v1/jp/regulated-entities/verify \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-company-verify/1.3.0" \
  -H "Content-Type: application/json" \
  -d '{"corporate_number": "1234567890123", "categories": ["funds_transfer_business"], "response_language": "en"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/v1/jp/regulated-entities/verify",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-company-verify/1.3.0"},
    json={"corporate_number": "1234567890123", "categories": ["funds_transfer_business"], "response_language": "en"},
    timeout=60,
)
print(response.status_code, response.json())
```
