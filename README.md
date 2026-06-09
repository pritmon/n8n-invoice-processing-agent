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

## How to test

1. Import `invoice-workflow.json` into your n8n instance
2. Add your OpenAI API key on the Chat Model node
3. Connect your Gmail account on the trigger and send nodes
4. On the **Extract PDF text** node, set Input Binary Field to `attachment_0`
5. Publish the workflow
6. Send yourself an email with subject containing "Invoice" and attach any PDF invoice
7. Wait up to 1 minute — the Gmail trigger polls every minute
8. Check your inbox for either a REVIEW REQUIRED or AUTO APPROVED email

**To trigger REVIEW REQUIRED:** send an invoice with total over INR 50,000
**To trigger AUTO APPROVED:** send an invoice with total under INR 50,000
