# JPVerify skills — Japan business data for AI agents

Agent skills and a Claude Code plugin for [JPVerify](https://www.jpverify.com/?ref=github): verify Japanese companies
(法人番号) and qualified-invoice T-numbers (インボイス), OCR Japanese invoices, receipts and business cards,
and more — with your agent choosing the right call and keeping costs in check.

```bash
npx skills add japanverify/jpverify-skills
```

## Skills

| Skill | What it does |
|---|---|
| [`jpverify`](skills/jpverify/SKILL.md) | JPVerify: Japan business data for AI agents — verify Japanese companies (法人番号), qualified-invoice T-numbers (インボイス), invoice issuers and suppliers; OCR Japanese invoices, receipts and business cards; normalize and validate Japanese addresses; plus JP PINT e-invoice validation, EU VAT/Peppol, UK and SEC cross-border checks. |
| [`jp-invoice-number-check`](skills/jp-invoice-number-check/SKILL.md) | Check Japanese qualified-invoice registration numbers (T-numbers, 適格請求書発行事業者登録番号, インボイス登録番号) against the National Tax Agency registry with JPVerify, and compare an invoice issuer's name and address with the registered record. |
| [`jp-company-verify`](skills/jp-company-verify/SKILL.md) | Verify Japanese companies against official registries with JPVerify: corporate number (法人番号) lookup by number or company name, active/closed status, registered address, a company pre-check report with risk flags, EDINET filings, and Financial Services Agency licence checks. |
| [`jp-business-document-ocr`](skills/jp-business-document-ocr/SKILL.md) | Read Japanese business documents with JPVerify: billing invoice (請求書) OCR, receipt (領収書・レシート) OCR and business card (名刺) OCR into structured JSON, with local checks of dates, tax, totals and T-numbers, and optional verification of the issuer against National Tax Agency registries. |
| [`jp-address`](skills/jp-address/SKILL.md) | Normalize, parse, validate and geocode Japanese addresses (住所の正規化・解析・検証) with JPVerify, and look up Japanese postal codes (郵便番号) — down to 丁目, block and building level, with the evidence behind each match. |
| [`jp-supplier-due-diligence`](skills/jp-supplier-due-diligence/SKILL.md) | Run a pre-payment supplier and counterparty check with JPVerify: combine Japanese corporate-number, qualified-invoice (T-number) and address evidence for a supplier, add FSA licence and EDINET checks when relevant, and for foreign counterparties check UK Companies House, EU VAT (VIES), Peppol participant IDs and SEC EDGAR filings. |
| [`jp-pint-invoice-validation`](skills/jp-pint-invoice-validation/SKILL.md) | Validate Japanese Peppol e-invoices (JP PINT 1.1.3 UBL Invoice XML) with JPVerify against the official OASIS UBL 2.1 XSD and OpenPeppol JP PINT Schematron, optionally checking the seller's qualified-invoice T-number in the National Tax Agency registry. |

## Install

| Agent | How |
|---|---|
| Any agent supporting skills (skills.sh) | `npx skills add japanverify/jpverify-skills` |
| Claude Code (plugin: skills + MCP server) | `/plugin marketplace add japanverify/jpverify-skills` then `/plugin install jpverify@jpverify` |
| Claude Code (skills only) | copy `skills/<name>/` to `~/.claude/skills/` |
| Codex | copy `skills/<name>/` to `~/.codex/skills/` |
| OpenClaw | search `jpverify` on ClawHub (once published) |

## Pay per call — two ways

- **API key (cards):** create a free account at https://www.jpverify.com/signup?ref=github (starter credit included),
  create a key and set `JPVERIFY_API_KEY`. Failed or invalid calls are refunded automatically.
  Docs: https://api.jpverify.com/docs/api-key
- **x402 (no account):** agents with a wallet pay each call in USDC on Base. Docs: https://api.jpverify.com/docs/x402

Prices are listed in each skill (from $0.001 per call) and at https://www.jpverify.com/#pricing.
Without a key or wallet the skills use the free `jpverify_guide` tool and point you to sign-up.

## MCP server

`https://api.jpverify.com/mcp` (Streamable HTTP). The plugin configures it automatically: save your API key in
`/plugin` → JPVerify → Configure (kept in the system keychain). Manually:

```bash
claude mcp add --transport http jpverify https://api.jpverify.com/mcp --header "Authorization: Bearer $JPVERIFY_API_KEY"
```

Also listed in the official MCP Registry as `com.jpverify/jpverify` ([japanverify/jpverify-mcp](https://github.com/japanverify/jpverify-mcp)).

## Safety

Instruction-only skills: no scripts, no bundled credentials. Every network call goes to `api.jpverify.com`
and is documented with its price. Results are public-registry pre-checks, not KYB/AML or legal/tax advice.

## License

MIT. Generated from the JPVerify API catalog (version 1.0.5); report issues at https://github.com/japanverify/jpverify-skills/issues
or service@jpverify.com. Privacy policy: https://api.jpverify.com/en/privacy · Terms: https://api.jpverify.com/en/terms
