# n8n Invoice Processing Agent

A live n8n workflow that monitors Gmail for invoice emails, extracts all fields
from PDF attachments using GPT-4o-mini, validates against business rules, and
automatically routes for human review or auto-approval.

## What it does

1. Gmail Trigger watches inbox for emails with PDF attachments
2. Extracts text from the PDF invoice
3. AI Agent (gpt-4o-mini) extracts all invoice fields — vendor, total, GST, due date, line items
4. Validates 5 business rules:
   - Amount over INR 50,000 → requires human review
   - Missing tax amount → flagged
   - Missing invoice number → flagged
   - Missing vendor GSTIN → flagged
   - Overdue invoice → flagged
5. Routes to REVIEW REQUIRED or AUTO APPROVED email accordingly
6. Logs every invoice (number, vendor, total, status, timestamp)

## Output

**REVIEW REQUIRED** — red email listing all issues found
**AUTO APPROVED** — green email confirming the invoice passed all checks

## Stack
- n8n Cloud
- GPT-4o-mini (OpenAI)
- LangChain (via n8n AI Agent node)
- Gmail API (trigger + send)

## How to import
1. Import `invoice-workflow.json` into n8n
2. Add OpenAI credential on the Chat Model node
3. Connect Gmail OAuth on the trigger and send nodes
4. Set the binary field on Extract PDF text node to `attachment_0`
5. Publish and send yourself a test invoice email
