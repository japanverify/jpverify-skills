# jp-pint-invoice-validation — request examples

Replace `<YOUR_JPVERIFY_API_KEY>` with your key (create one at https://www.jpverify.com/signup?ref=jp-pint-invoice-validation) and run the command yourself; inside Claude Code prefer the plugin's MCP tools. Without a key the API answers HTTP 402 with the x402 price; see https://api.jpverify.com/docs/x402 for x402 SDK code.

## `POST /v1/invoices/validate-jp-pint` — $0.02

```bash
# invoice.xml: your JP PINT 1.1.3 UBL Invoice
jq -n --rawfile xml invoice.xml '{xml: $xml, registry_enhance: true}' |
  curl -X POST https://api.jpverify.com/v1/invoices/validate-jp-pint \
    -H "Authorization: Bearer <YOUR_JPVERIFY_API_KEY>" \
    -H "X-JPVerify-Client: jp-pint-invoice-validation/1.1.0" \
    -H "Content-Type: application/json" \
    --data-binary @-
```

```python
import pathlib, requests

response = requests.post(
    "https://api.jpverify.com/v1/invoices/validate-jp-pint",
    headers={"Authorization": "Bearer <YOUR_JPVERIFY_API_KEY>",
             "X-JPVerify-Client": "jp-pint-invoice-validation/1.1.0"},
    json={"xml": pathlib.Path("invoice.xml").read_text(encoding="utf-8"), "registry_enhance": True},
    timeout=60,
)
print(response.status_code, response.json())
```
