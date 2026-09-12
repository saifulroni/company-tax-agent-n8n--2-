# Setup Guide

## 1. Prerequisites

- A working n8n instance
- Google account with access to the source spreadsheet and regulations document
- OpenAI model credential available in n8n
- Gmail OAuth credential
- Permission to read/write the required Google Sheets and Google Docs resources

## 2. Import the workflow

In n8n:

1. Open **Workflows**.
2. Choose **Import from File**.
3. Select `workflows/company-tax-agent.json`.
4. Open the imported workflow.
5. Review every credential and external-resource reference before testing.

## 3. Configure credentials

Create/select credentials for:

### Google Sheets

Used by:

- Read Income Ledger
- Append Statement Rows
- Tool: Income Ledger
- Tool: Taxation Statements

### Google Docs

Used by:

- Read Tax Regulations
- Tool: Tax Regulations Doc

### OpenAI

Used by:

- OpenAI Model (Chat)
- OpenAI Model (Calc)

The workflow currently specifies `gpt-5-mini` in the model nodes. Confirm that the model is available to your n8n/OpenAI configuration before execution.

### Gmail

Used by:

- Send Email To CFO

## 4. Configure Google resources

The public workflow intentionally uses placeholders.

Replace:

```text
REPLACE_WITH_SPREADSHEET_ID
REPLACE_WITH_REGULATIONS_DOC_ID
```

with your own resources.

The workflow expects two logical sheets:

```text
compnay income statements
taxation statement
```

The first name is retained from the original workflow. You can rename the sheet, but then update every corresponding Google Sheets node/tool.

## 5. Configure the CFO email

Replace:

```text
YOUR_CFO_EMAIL@example.com
```

in **Send Email To CFO** with the intended recipient.

For a public portfolio repository, keep the repository copy as a placeholder and configure the real address only inside your private n8n instance.

## 6. Prepare the regulation document

The calculation agent is explicitly instructed to use only the supplied regulation text and ledger rows.

The Q&A agent is also instructed to avoid guessing and to state when information is unavailable.

For a real deployment, the regulation source should have:

- a clear version/date
- effective dates
- unambiguous clauses
- review/approval by a qualified tax professional

## 7. Test the Q&A agent

Use the Chat Trigger and test questions such as:

```text
What transactions were treated as deductible expenses?
Which regulation clause supports transaction TXN-001?
What is the latest calculated tax liability?
Why was this transaction marked non-deductible?
```

The agent should rely on its connected tools instead of inventing facts.

## 8. Test the daily pipeline

Before activating:

1. Run the workflow manually.
2. Verify ledger retrieval.
3. Verify regulation retrieval.
4. Inspect the structured output.
5. Confirm every ledger row is represented.
6. Check the tax totals.
7. Check the taxation statement sheet.
8. Check the generated email.
9. Confirm the recipient is correct.

## 9. Production safeguards

Do not rely on an LLM alone for legally or financially material calculations.

Recommended architecture:

```text
LLM classification
       ↓
Rule validation
       ↓
Deterministic arithmetic
       ↓
Exception detection
       ↓
Human approval
       ↓
Reporting / filing
```

This makes the system easier to audit and safer to operate.
