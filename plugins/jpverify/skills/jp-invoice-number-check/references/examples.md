# jp-invoice-number-check — request examples

Replace `<YOUR_JPVERIFY_API_KEY>` with your key (create one at https://www.jpverify.com/signup?ref=jp-invoice-number-check) and run the command yourself; inside Claude Code prefer the plugin's MCP tools. Without a key the API answers HTTP 402 with the x402 price; see https://api.jpverify.com/docs/x402 for x402 SDK code.

## `POST /jp-invoice/verify` — $0.01

```bash
curl -X POST https://api.jpverify.com/jp-invoice/verify \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-invoice-number-check/1.0.3" \
  -H "Content-Type: application/json" \
  -d '{"registration_number": "T1013301046627", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-invoice/verify",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-invoice-number-check/1.0.3"},
    json={"registration_number": "T1013301046627", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-invoice/check` — $0.03

```bash
curl -X POST https://api.jpverify.com/jp-invoice/check \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-invoice-number-check/1.0.3" \
  -H "Content-Type: application/json" \
  -d '{"registration_number": "T1013301046627", "supplier_name": "株式会社ABC", "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-invoice/check",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-invoice-number-check/1.0.3"},
    json={"registration_number": "T1013301046627", "supplier_name": "株式会社ABC", "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```

## `POST /jp-invoice/verify-batch` — $0.05

```bash
curl -X POST https://api.jpverify.com/jp-invoice/verify-batch \
  -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
  -H "X-JPVerify-Client: jp-invoice-number-check/1.0.3" \
  -H "Content-Type: application/json" \
  -d '{"registration_numbers": ["T1013301046627"], "response_language": "both"}'
```

```python
import requests

response = requests.post(
    "https://api.jpverify.com/jp-invoice/verify-batch",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-invoice-number-check/1.0.3"},
    json={"registration_numbers": ["T1013301046627"], "response_language": "both"},
    timeout=60,
)
print(response.status_code, response.json())
```
