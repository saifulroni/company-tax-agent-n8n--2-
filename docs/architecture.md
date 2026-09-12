# Architecture

## Two-agent architecture

The workflow contains two distinct AI behaviors.

### 1. Tax Q&A Agent

Entry point:

```text
Chat Trigger
    ↓
Tax Q&A Agent
    ├── OpenAI Model (Chat)
    ├── Chat Memory
    ├── Income Ledger Tool
    ├── Taxation Statements Tool
    └── Tax Regulations Tool
```

The agent can answer questions about company income, expenses, tax position, historical statements, and the supplied regulations.

Its system instructions require it to cite the ledger rows or regulation clauses used and to avoid guessing.

### 2. Tax Calculation Agent

Entry point:

```text
Daily Schedule Trigger
    ↓
Read Income Ledger
    ↓
Read Tax Regulations
    ↓
Calculate Tax Statement
    ├── OpenAI Model (Calc)
    └── Statement Output Parser
    ↓
    ├── Explode Line Items → Append Statement Rows
    └── Build Email Report → Send Email To CFO
```

The calculation agent receives regulation text and ledger rows and returns a structured statement.

## Structured output

The parser requires:

```json
{
  "period": "YYYY-MM",
  "line_items": [],
  "gross_income": 0,
  "total_deductions": 0,
  "taxable_income": 0,
  "tax_rate": 0,
  "tax_liability": 0
}
```

Each line item contains:

```json
{
  "TransactionID": "TXN-001",
  "amount": 0,
  "category": "example",
  "taxable_or_deductible": "taxable income",
  "regulation_ref": "Clause X",
  "notes": "Explanation"
}
```

## Design principle

The workflow uses the LLM for natural-language interpretation and tax classification while retaining structured output and spreadsheet storage.

For production, deterministic calculation and human approval should be placed after AI classification.
